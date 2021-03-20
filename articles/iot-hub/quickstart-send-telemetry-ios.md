---
title: Szybki Start — wysyłanie danych telemetrycznych do usługi Azure IoT Hub — szybki start (iOS)
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację systemu iOS wysyłającą symulowane dane telemetryczne do centrum IoT oraz odczytującą dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
ms.date: 04/03/2019
ms.openlocfilehash: 4af985d93c5b02ab20df97abd5fa0371edf889f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624256"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym artykule opisano wysyłanie danych telemetrycznych z aplikacji urządzenia symulowanego do usługi IoT Hub, a następnie wyświetlanie tych danych z poziomu aplikacji zaplecza.

W tym artykule używana jest wstępnie napisana aplikacja języka Swift wysyłająca dane telemetryczne oraz narzędzie interfejsu wiersza polecenia odczytujące dane telemetryczne z usługi IoT Hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz przykładowy kod z [przykładów dla platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

- Najnowsza wersja środowiska [XCode](https://developer.apple.com/xcode/) korzystająca z najnowszej wersji zestawu SDK systemu iOS. Ten przewodnik Szybki Start został przetestowany z systemami XCode 10,2 i iOS 12,2.

- Najnowsza wersja menedżera [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym przewodniku szybki start używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **myiOSdevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **myiOSdevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Przykładowa aplikacja jest uruchamiana na urządzeniu z systemem iOS, które łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności. 

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

W lokalnym oknie terminalu przejdź do folderu Azure-IoT-Samples-iOS pobranego w ramach wymagań wstępnych. Następnie przejdź do przykładowego projektu:

```sh
cd quickstart/sample-device
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile**:

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności. 

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji 

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń projekt **MQTT Client Sample**, a następnie rozwiń folder o takiej samej nazwie.  
3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 
4. Wyszukaj zmienną **ConnectionString** i zaktualizuj wartość przy użyciu parametrów połączenia urządzenia, które zostały wcześniej zanotowane.
5. Zapisz zmiany. 
6. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Skompiluj i uruchom** lub kombinacji klawiszy **Command + R**. 

   ![Uruchamianie projektu](media/quickstart-send-telemetry-ios/run-sample.png)

7. Po otwarciu emulatora wybierz pozycję **Start** w przykładowej aplikacji.

Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe w momencie wysyłania przez aplikację symulowanych danych telemetrycznych do centrum IoT:

   ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Przykładowa aplikacja uruchomiona w emulatorze środowiska XCode wyświetla dane dotyczące komunikatów wysłanych z urządzenia. Dane możesz wyświetlić także za pośrednictwem centrum IoT, gdy są odbierane. Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez rozszerzenie danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

Poniższy zrzut ekranu przedstawia typ danych telemetrycznych wyświetlanych w lokalnym oknie terminalu:

![Wyświetlanie danych telemetrycznych](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do centrum z urządzenia z systemem iOS i odczytuje dane telemetryczne z centrum. 

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-node.md)
