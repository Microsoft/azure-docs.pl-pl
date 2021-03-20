---
title: Samouczek dotyczący zabezpieczeń X. 509 na platformie Azure IoT Hub | Microsoft Docs
description: Zacznij korzystać z zabezpieczeń opartych na X. 509 w usłudze Azure IoT Hub w symulowanym środowisku.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: a3e328418a0f111cd0b985310ea6dc497999772d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909798"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurowanie zabezpieczeń X.509 w usłudze Azure IoT Hub

W tym samouczku przedstawiono kroki potrzebne do zabezpieczenia usługi Azure IoT Hub przy użyciu *uwierzytelniania certyfikatu X. 509*. Na potrzeby ilustracji korzystamy z narzędzia typu "open source" OpenSSL do tworzenia certyfikatów lokalnie na komputerze z systemem Windows. Zalecamy korzystanie z tego samouczka tylko do celów testowych. W środowisku produkcyjnym należy zakupić certyfikaty z *głównego urzędu certyfikacji (CA)*. Ponadto w obszarze produkcja upewnij się, że masz strategię do obsługi przerzucania certyfikatów, gdy certyfikat urządzenia lub certyfikat urzędu certyfikacji wygaśnie.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga przygotowań następujących zasobów:

* Utworzono Centrum IoT Hub przy użyciu subskrypcji platformy Azure. Szczegółowe instrukcje można znaleźć w temacie [Tworzenie usługi IoT Hub w portalu](iot-hub-create-through-portal.md) .

* Masz zainstalowany [program Visual studio 2017 lub Visual studio 2019](https://www.visualstudio.com/vs/) .

## <a name="get-x509-ca-certificates"></a>Pobierz certyfikaty urzędu certyfikacji X. 509

Zabezpieczenia oparte na certyfikatach X. 509 w IoT Hub wymagają rozpoczęcia od [łańcucha certyfikatów x. 509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), który obejmuje certyfikat główny, a także wszystkie certyfikaty pośrednie aż do certyfikatu liścia.

Możesz wybrać jeden z następujących sposobów uzyskiwania certyfikatów:

* Zakup certyfikatów X. 509 z *głównego urzędu certyfikacji (CA)*. Ta metoda jest zalecana w środowiskach produkcyjnych.

* Utwórz własne certyfikaty X. 509 przy użyciu narzędzi innych firm, takich jak [OpenSSL](https://www.openssl.org/). Ta technika jest przeznaczona dla celów testowych i programistycznych. Więcej informacji na temat generowania certyfikatów testów urzędu certyfikacji przy użyciu programu PowerShell lub bash można znaleźć w temacie [Zarządzanie certyfikatami dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) . W pozostałej części tego samouczka są używane certyfikaty testów urzędu certyfikacji wygenerowane zgodnie z instrukcjami zawartymi w temacie [Zarządzanie certyfikatami testów urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Generuj [certyfikat pośredniego urzędu certyfikacji X. 509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) podpisany przez istniejący certyfikat głównego urzędu certyfikacji i przekaż go do centrum. Po przekazaniu i zweryfikowaniu certyfikatu pośredniego można go użyć w miejscu certyfikatu głównego urzędu certyfikacji wymienionego poniżej. Narzędzia takie jak OpenSSL ([OpenSSL REQ](https://www.openssl.org/docs/man1.1.0/man1/req.html) i [OpenSSL CA](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) mogą służyć do generowania i podpisywania certyfikatu pośredniego urzędu certyfikacji.

> [!NOTE]
> Nie przekazuj certyfikatu głównego innej firmy, jeśli nie jest on unikatowy, ponieważ umożliwi to innym klientom trzeciej firmy Łączenie urządzeń z IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Rejestrowanie certyfikatów urzędu certyfikacji X. 509 w usłudze IoT Hub

W tych krokach pokazano, jak dodać nowy urząd certyfikacji do centrum IoT Hub za pomocą portalu. W przypadku korzystania z uwierzytelniania urzędu certyfikacji X. 509 upewnij się, że nowy certyfikat został zarejestrowany przed wygaśnięciem istniejącego certyfikatu w ramach strategii przerzucania certyfikatów.

> [!NOTE]
> Maksymalna liczba certyfikatów urzędu certyfikacji X. 509, które można zarejestrować w usłudze IoT Hub, to 25. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia dotyczące usługi Azure IoT Hub](iot-hub-devguide-quotas-throttling.md).

1. W Azure Portal przejdź do centrum IoT Hub, a następnie wybierz pozycję **Ustawienia**  >  **Certyfikaty** dla centrum.

1. Wybierz pozycję **Dodaj** , aby dodać nowy certyfikat.

1. W polu **Nazwa certyfikatu** wprowadź przyjazną nazwę wyświetlaną i wybierz plik certyfikatu utworzony w poprzedniej sekcji na komputerze.

1. Po otrzymaniu powiadomienia o pomyślnym przekazaniu certyfikatu wybierz pozycję **Zapisz**.

    ![Przekazywanie certyfikatu](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Certyfikat zostanie wyświetlony na liście certyfikaty ze stanem **niezweryfikowane**.

1. Wybierz właśnie dodany certyfikat, aby wyświetlić **Szczegóły certyfikatu**, a następnie wybierz pozycję **Generuj kod weryfikacyjny**.

   ![Weryfikuj certyfikat](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Skopiuj **kod weryfikacyjny** do Schowka. Służy do weryfikacji własności certyfikatu.

1. Wykonaj krok 3 w temacie [Zarządzanie certyfikatami testów urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Ten proces podpisuje kod weryfikacyjny z kluczem prywatnym skojarzonym z certyfikatem urzędu certyfikacji X. 509, który generuje podpis. Dostępne są narzędzia umożliwiające wykonanie tego procesu podpisywania, na przykład OpenSSL. Ten proces jest znany jako [dowód posiadania](https://tools.ietf.org/html/rfc5280#section-3.1).

1. W obszarze **Szczegóły certyfikatu** w obszarze **certyfikat weryfikacyjny plik PEM lub CER** Znajdź i Otwórz plik sygnatury. Następnie wybierz pozycję **Weryfikuj**.

   Stan certyfikatu zmieni się na **zweryfikowane**. Wybierz pozycję **Odśwież** , jeśli certyfikat nie zostanie automatycznie zaktualizowany.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Tworzenie urządzenia X. 509 dla Centrum IoT Hub

1. W Azure Portal przejdź do centrum IoT Hub, a następnie wybierz pozycję **Explorer**  >  **urządzenia IoT**.

1. Wybierz pozycję **Nowy** , aby dodać nowe urządzenie.

1. W polu **Identyfikator urządzenia** wprowadź przyjazną nazwę wyświetlaną. W obszarze **Typ uwierzytelniania** wybierz **podpisany urząd certyfikacji X. 509**, a następnie wybierz pozycję **Zapisz**.

   ![Tworzenie urządzenia X. 509 w portalu](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Uwierzytelnianie urządzenia X. 509 za pomocą certyfikatów X. 509

Aby uwierzytelnić urządzenie X. 509, należy najpierw podpisać urządzenie przy użyciu certyfikatu urzędu certyfikacji. Podpisywanie urządzeń liściowych jest zwykle wykonywane w zakładzie produkcyjnym, gdzie zostały odpowiednio włączone narzędzia produkcyjne. Gdy urządzenie przejdzie od jednego producenta do drugiego, Akcja podpisywania każdej z producentów jest przechwytywana jako certyfikat pośredni w łańcuchu. Wynikiem jest łańcuch certyfikatów z certyfikatu urzędu certyfikacji do certyfikatu liścia urządzenia. Krok 4 w temacie [Zarządzanie certyfikatami przetestowanych urzędów certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) powoduje wygenerowanie certyfikatu urządzenia.

Następnie pokażemy, jak utworzyć aplikację w języku C# w celu symulowania urządzenia X. 509 zarejestrowanego dla Centrum IoT. Będziemy wysyłać wartości temperatury i wilgotności z symulowanego urządzenia do centrum. W tym samouczku utworzymy tylko aplikację urządzenia. Jest to pozostawione jako ćwiczenie dla czytelników, aby utworzyć aplikację usługi IoT Hub, która będzie wysyłać odpowiedzi na zdarzenia wysyłane przez to symulowane urządzenie. W aplikacji C# założono, że wykonano kroki [zarządzania certyfikatami testów urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Otwórz program Visual Studio, wybierz pozycję **Utwórz nowy projekt**, a następnie wybierz szablon projektu **aplikacja konsoli (.NET Framework)** . Wybierz opcję **Dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nazwij projekt *SimulateX509Device*, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie projektu urządzenia X. 509 w programie Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **SimulateX509Device** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet** wybierz pozycję **Przeglądaj** i Wyszukaj, a następnie wybierz pozycję **Microsoft. Azure. Devices. Client**. Wybierz pozycję **Zainstaluj**.

   ![Dodaj pakiet NuGet zestawu SDK urządzenia w programie Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet zestawu SDK urządzenia usługi Azure IoT oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Dodaj następujące pola do klasy **program** :

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Użyj przyjaznej nazwy urządzenia, która została użyta w poprzedniej sekcji zamiast _<your_device_id>_.

1. Dodaj następującą funkcję, aby utworzyć liczbę losową dla temperatury i wilgotności i wysłać te wartości do centrum:

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

1. Na koniec Dodaj następujące wiersze kodu do funkcji **Main** , zastępując symbole zastępcze _Identyfikator urządzenia_, _nazwę pliku-IoT-Hub-Name_ i _bezwzględną ścieżkę do urządzenia-PFX_ , zgodnie z wymaganiami Instalatora.

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

   Ten kod nawiązuje połączenie z Centrum IoT Hub przez utworzenie parametrów połączenia dla urządzenia X. 509. Po pomyślnym nawiązaniu połączenia wysyła on do centrum zdarzenia temperatury i wilgotności i czeka na odpowiedź.

1. Uruchom aplikację. Ponieważ ta aplikacja uzyskuje dostęp do pliku *PFX* , może być konieczne uruchomienie tej aplikacji jako administrator.

   1. Utwórz rozwiązanie Visual Studio.

   1. Otwórz nowe okno wiersza polecenia, korzystając z opcji **Uruchom jako administrator**.  

   1. Przejdź do folderu, który zawiera Twoje rozwiązanie, a następnie przejdź do ścieżki *bin/debug* w folderze rozwiązania.

   1. Uruchom **SimulateX509Device.exe** aplikacji z wiersza polecenia.

   Urządzenie powinno być widoczne, aby pomyślnie nawiązać połączenie z centrum i wysłać zdarzenia.

   ![Uruchom aplikację urządzenia](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zabezpieczania rozwiązania IoT, zobacz:

* [Najlepsze rozwiązania z zakresu zabezpieczeń IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Architektura zabezpieczeń IoT](../iot-fundamentals/iot-security-architecture.md)

* [Zabezpieczanie wdrożenia IoT](../iot-fundamentals/iot-security-deployment.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)