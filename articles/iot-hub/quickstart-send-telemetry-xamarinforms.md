---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start | Microsoft Docs
description: W tym przewodniku szybki start uruchomisz przykładową aplikację Xamarin. Forms w celu wysyłania symulowanych danych telemetrycznych do centrum IoT i odczytywania danych telemetrycznych z usługi IoT Hub na potrzeby przetwarzania w chmurze.
author: cmaneu
manager: philmea
ms.author: chmaneu
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 07/01/2020
ms.openlocfilehash: 4178ee38ca179f179824a13130121b997f4f5f14
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807824"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-xamarinforms"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub (Xamarin. Forms)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym artykule opisano wysyłanie danych telemetrycznych z aplikacji urządzenia symulowanego do usługi IoT Hub, a następnie wyświetlanie tych danych z poziomu aplikacji zaplecza.

W tym artykule jest stosowana wstępnie zapisywana aplikacja Xamarin. Forms, która umożliwia wysyłanie danych telemetrycznych i narzędzi interfejsu wiersza polecenia w celu odczytu danych telemetrycznych z IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz przykładowy kod z [przykładów dla platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-xamarin/archive/master.zip)

- Najnowsza wersja programu [Visual Studio 2019](https://visualstudio.microsoft.com/) lub [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/) z zainstalowanymi narzędziami Xamarin. Forms. Ten przewodnik Szybki Start został przetestowany przy użyciu programu Visual Studio 16.6.0.

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

- Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IoT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **myXamarinDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **myXamarinDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myXamarinDevice
   ```

1. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myXamarinDevice --output table
   ```

   Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myXamarinDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Przykładowa aplikacja jest uruchamiana w systemie Windows — za pośrednictwem aplikacji platformy UWP — urządzenia z systemem iOS lub symulatorem oraz na urządzeniu z systemem Android lub symulatorze, który łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury i wilgotności. 

1. Otwórz przykładowy obszar roboczy w programie Visual Studio lub Visual Studio dla komputerów Mac.
2. Rozwiń projekt **SimulatedDevice** .  
3. Otwórz **IoTHubService.cs** do edycji w programie Visual Studio. 
4. Wyszukaj zmienną **_iotHubConnectionString** i zaktualizuj wartość przy użyciu parametrów połączenia urządzenia, które zostały wcześniej zanotowane.
5. Zapisz zmiany. 
6. Uruchom projekt w emulatorze urządzenia lub rzeczywistym urządzeniu za pomocą przycisku **Kompiluj i uruchom** lub klawisz skrótu **F5** w systemie Windows lub **Command + r** na komputerze Mac. 

   ![Uruchamianie projektu](media/quickstart-send-telemetry-xamarinforms/run-sample.png)

7. Po otwarciu emulatora wybierz pozycję **Start** w przykładowej aplikacji.

Poniższy zrzut ekranu przedstawia przykład danych wyjściowych, gdy aplikacja wysyła symulowane dane telemetryczne do centrum IoT Hub: ![ Uruchamianie symulowanego urządzenia](media/quickstart-send-telemetry-xamarinforms/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Przykładowa aplikacja uruchomiona w emulatorze środowiska XCode wyświetla dane dotyczące komunikatów wysłanych z urządzenia. Dane możesz wyświetlić także za pośrednictwem centrum IoT, gdy są odbierane. Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myXamarinDevice --hub-name {YourIoTHubName}
```

Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez rozszerzenie danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

Poniższy zrzut ekranu przedstawia typ danych telemetrycznych wyświetlanych w oknie terminalu lokalnego: ![ Wyświetlanie telemetrii](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do centrum z aplikacji Xamarin. Forms i odczytuje dane telemetryczne z centrum. 

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-node.md)
