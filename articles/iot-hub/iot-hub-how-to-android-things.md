---
title: Opracowywanie platformy dla systemu Android przy użyciu zestawów SDK usługi Azure IoT | Microsoft Docs
description: Przewodnik dla deweloperów — informacje na temat sposobu tworzenia aplikacji w systemie Android przy użyciu zestawów SDK IoT Hub platformy Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 1d824c94a2c5751013b78c69ed5abd048c50856b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151730"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Opracowywanie platformy dla systemu Android przy użyciu zestawów SDK usługi Azure IoT

[Usługa Azure IoT Hub SDK](./iot-hub-devguide-sdks.md) zapewnia obsługę pierwszej warstwy dla popularnych platform, takich jak systemy Windows, Linux, OSX, MBED i platformy mobilne, takie jak Android i iOS.  W ramach naszych zobowiązań, aby zapewnić większy wybór i elastyczność w ramach wdrożeń IoT, zestaw SDK Java obsługuje również platformę [Android](https://developer.android.com/things/) .  Deweloperzy mogą korzystać z zalet systemu operacyjnego Android na stronie urządzenia, jednocześnie korzystając z [usługi Azure IoT Hub](about-iot-hub.md) jako centralnego centrum komunikatów, które skaluje się do milionów jednocześnie połączonych urządzeń.

Ten samouczek zawiera opis kroków tworzenia aplikacji po stronie urządzenia w systemie Android przy użyciu zestawu SDK Java usługi Azure IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* Obsługiwane przez system Android sprzęt z systemem Android działa.  Aby zapoznać się z dokumentacją dotyczącą systemu Android, możesz wykonać czynności opisane w [dokumentacji](https://developer.android.com/things/get-started/kits#flash-at) dotyczącej systemu Android.  Upewnij się, że urządzenie z systemem Android jest połączone z Internetem przy użyciu podstawowych urządzeń peryferyjnych, takich jak klawiatura, wyświetlacz i mysz.  W tym samouczku jest stosowane Raspberry Pi 3.

* Najnowsza wersja [Android Studio](https://developer.android.com/studio/)

* Najnowsza wersja usługi [git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby dodać rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT.

   **MyAndroidThingsDevice** : jest to nazwa podaną dla zarejestrowanego urządzenia. Użyj MyAndroidThingsDevice, jak pokazano. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Uruchom następujące polecenia w Azure Cloud Shell, aby uzyskać *Parametry połączenia urządzenia* dla zarejestrowanego urządzenia. Zastąp `YourIoTHubName` wartość poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="building-an-android-things-application"></a>Kompilowanie aplikacji dla systemu Android

1. Pierwszym krokiem tworzenia aplikacji dla systemu Android jest łączenie się z urządzeniami z systemem Android. Podłącz urządzenie z systemem Android do ekranu i połącz je z Internetem. W systemie Android można znaleźć [dokumentację](https://developer.android.com/things/get-started/kits) dotyczącą sposobu nawiązywania połączenia z siecią Wi-Fi. Po nawiązaniu połączenia z Internetem Zanotuj adres IP wymieniony w obszarze sieci.

2. Użyj narzędzia [ADB](https://developer.android.com/studio/command-line/adb) , aby nawiązać połączenie z urządzeniem z systemem Android przy użyciu adresu IP wymienionego powyżej. Sprawdź połączenie przy użyciu tego polecenia z terminalu. Urządzenia powinny być widoczne na liście jako "połączone".

   ```
   adb devices
   ```

3. Pobierz nasze przykładowe elementy dla systemu Android/Android z tego [repozytorium](https://github.com/Azure-Samples/azure-iot-samples-java) lub użyj narzędzia Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. W Android Studio Otwórz projekt systemu Android w lokalizacji "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Otwórz plik Gradle. Properties i Zastąp ciąg "Device_connection_string" wymienionymi wcześniej parametrami połączenia urządzenia.
 
6. Kliknij polecenie Uruchom ponownie debugowanie i wybierz urządzenie, aby wdrożyć ten kod na urządzeniach z systemem Android.

7. Po pomyślnym uruchomieniu aplikacji zobaczysz aplikację działającą na urządzeniu z systemem Android. Ta przykładowa aplikacja wysyła losowo wygenerowane odczyty temperatury.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Dane można wyświetlić za pomocą Centrum IoT w miarę ich odbierania. Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

* Informacje o [sposobach zarządzania łącznością i niezawodną obsługą komunikatów](iot-hub-reliability-features-in-sdks.md) przy użyciu zestawów SDK IoT Hub.
* Dowiedz się więcej na temat [opracowywania platform mobilnych](iot-hub-how-to-develop-for-mobile-devices.md) , takich jak iOS i Android.
* [Obsługa platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)