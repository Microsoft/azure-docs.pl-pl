---
title: Samouczek — testowanie możliwości certyfikatów X.509 w celu uwierzytelniania urządzeń w Azure IoT Hub | Microsoft Docs
description: Samouczek — testowanie certyfikatów X.509 w celu uwierzytelniania w Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379438"
---
# <a name="tutorial-testing-certificate-authentication"></a>Samouczek: testowanie uwierzytelniania certyfikatu

Poniższy przykład kodu w języku C# umożliwia przetestowanie, czy certyfikat może uwierzytelnić urządzenie w IoT Hub. Przed uruchomieniem kodu testowego należy wykonać następujące czynności:

* Utwórz certyfikat głównego lub podrzędnego urzędu certyfikacji.
* Przekaż certyfikat urzędu certyfikacji do IoT Hub.
* Udowodnij, że posiadasz certyfikat urzędu certyfikacji.
* Dodaj urządzenie do swojego IoT Hub.
* Utwórz certyfikat urządzenia z tym samym identyfikatorem urządzenia, który jest Twoim urządzeniem.

## <a name="code-example"></a>Przykład kodu

Poniższy przykład kodu pokazuje, jak utworzyć aplikację w języku C# w celu symulowania urządzenia X.509 zarejestrowanego dla centrum IoT. Przykład wysyła wartości temperatury i wilgotności z symulowanego urządzenia do centrum. W tym samouczku utworzymy tylko aplikację urządzenia. Dla czytelników pozostało ćwiczenie w celu utworzenia aplikacji usługi IoT Hub, która będzie wysyłać odpowiedzi na zdarzenia wysyłane przez to symulowane urządzenie.

1. Otwórz Visual Studio, wybierz pozycję Utwórz nowy **projekt, a** następnie wybierz szablon projektu **Aplikacja konsolowa (.NET Framework).** Wybierz opcję **Dalej**.

1. Na **stronie Konfigurowanie nowego projektu** nadaj projektowi nazwę *SimulateX509Device,* a następnie wybierz pozycję **Utwórz.**

   ![Tworzenie projektu urządzenia X.509 w Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **SimulateX509Device,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet.**

1. W **witrynie Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj,** wyszukaj i wybierz **pozycję Microsoft.Azure.Devices.Client.** Wybierz pozycję **Zainstaluj**.

   ![Dodawanie pakietu NuGet zestawu SDK urządzenia w Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ten krok powoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet zestawu SDK urządzenia Usługi Azure IoT oraz jego zależności.

    Wprowadź i uruchom następujący kod:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

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
         }
    }
}
```