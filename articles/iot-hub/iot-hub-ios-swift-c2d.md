---
title: Komunikaty między chmurami a urządzeniem za pomocą usługi Azure IoT Hub (iOS) | Dokumenty firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia do urządzenia z centrum Usługi Azure IoT przy użyciu zestawów SDK usługi Azure IoT dla systemu iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: d8552391e8e8c389a44174595305b8f28224a833
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732535"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Wysyłanie wiadomości z chmury do urządzenia za pomocą usługi IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Wyślij [dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-ios.md) Szybki start pokazuje, jak utworzyć centrum IoT hub, aprowizować tożsamość urządzenia w nim i kod symulowanej aplikacji urządzenia, która wysyła komunikaty urządzenia do chmury.

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Z zaplecza rozwiązania wysyłaj komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzenia na urządzeniu.

* W rozwiązaniu zaplecza, żądanie potwierdzenia dostarczania *(opinie)* dla wiadomości wysyłanych do urządzenia z Usługi IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzenia można znaleźć w [sekcji wiadomości przewodnika dla deweloperów usługi IoT Hub.](iot-hub-devguide-messaging.md)

Na końcu tego artykułu uruchom dwa projekty Swift iOS:

* **przykładowe urządzenie**, ta sama aplikacja utworzona w [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-ios.md)hub , który łączy się z centrum IoT hub i odbiera komunikaty z chmury do urządzenia.

* **usługa próbkowania**, która wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pośrednictwem usługi IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym języka C, Java, Python i Javascript) za pośrednictwem zestawów SDK urządzeń IoT platformy Azure. Aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do kodu tego samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów usługi Azure IoT.](https://www.azure.com/develop/iot)

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Aktywne centrum IoT na platformie Azure.

* Przykładowy kod z [przykładów platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Najnowsza wersja środowiska [XCode](https://developer.apple.com/xcode/) korzystająca z najnowszej wersji zestawu SDK systemu iOS. Ten przewodnik Szybki start przetestowano przy użyciu środowiska XCode 9.3 i systemu iOS 11.3.

* Najnowsza wersja menedżera [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Symulowanie urządzenia IoT

W tej sekcji symulujesz urządzenie z systemem iOS z uruchomiona aplikacją Swift w celu odbierania komunikatów z chmury do urządzenia z centrum IoT Hub. 

Jest to przykładowe urządzenie utworzone w artykule [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT hub](quickstart-send-telemetry-ios.md). Jeśli masz już to uruchomione, możesz pominąć tę sekcję.

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

W oknie terminalu przejdź do folderu Azure-IoT-Samples-iOS pobranego w ramach wymagań wstępnych. Następnie przejdź do przykładowego projektu:

```sh
cd quickstart/sample-device
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile**:

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności.

### <a name="run-the-sample-device-application"></a>Uruchamianie przykładowej aplikacji urządzenia

1. Pobierz ciąg połączenia dla urządzenia. Ten ciąg można skopiować z [witryny Azure portal](https://portal.azure.com) w bloku szczegółów urządzenia lub pobrać go za pomocą następującego polecenia INTERFEJSU WIERSZA POLECENIA:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń przykładowy projekt **klienta MQTT,** a następnie folder o tej samej nazwie.  

3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 

4. Wyszukaj **zmienną connectionString** i zaktualizuj wartość za pomocą ciągu połączenia urządzenia skopiowanego w pierwszym kroku.

5. Zapisz zmiany. 

6. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Skompiluj i uruchom** lub kombinacji klawiszy **Command + R**.

   ![Uruchamianie projektu](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do wysyłania komunikatów z chmury do urządzenia za pośrednictwem centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-ios.md). Aby wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Symulowanie urządzenia serwisowego

W tej sekcji symulujesz drugie urządzenie z systemem iOS za pomocą aplikacji Swift, która wysyła komunikaty z chmury do urządzenia za pośrednictwem centrum IoT Hub. Ta konfiguracja jest przydatna w scenariuszach IoT, w których jeden iPhone lub iPad działa jako kontroler dla innych urządzeń z systemem iOS podłączonych do centrum IoT Hub.

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

Przejdź do folderu Przykłady usługi Azure IoT iOS pobranego w wymaganiach wstępnych. Następnie przejdź do przykładowego projektu usługi:

```sh
cd quickstart/sample-service
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile**:

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności.

### <a name="run-the-sample-service-application"></a>Uruchamianie przykładowej aplikacji serwisowej

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Rozwiń projekt **AzureIoTServiceSample,** a następnie rozwiń folder o tej samej nazwie.  

3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 

4. Wyszukaj zmienną **connectionString** i zaktualizuj wartość za pomocą parametry połączenia usługi skopiowanego wcześniej w [polu Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

5. Zapisz zmiany.

6. W xcode zmień ustawienia emulatora na inne urządzenie z systemem iOS niż używane do uruchamiania urządzenia IoT. XCode nie można uruchomić wiele emulatorów tego samego typu.

   ![Zmienianie urządzenia emulatora](media/iot-hub-ios-swift-c2d/change-device.png)

7. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Buduj i uruchom** lub klawisza kombi **+ r**.

   ![Uruchamianie projektu](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie wiadomości z chmury do urządzenia

Teraz można przystąpić do używania dwóch aplikacji do wysyłania i odbierania komunikatów z chmury do urządzenia.

1. W **przykładowej aplikacji dla systemu iOS uruchomionej** na symulowanym urządzeniu IoT kliknij przycisk **Start**. Aplikacja rozpoczyna wysyłanie komunikatów z urządzenia do chmury, ale również rozpoczyna nasłuchiwanie komunikatów z chmury do urządzenia.

   ![Wyświetlanie przykładowej aplikacji urządzenia IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. W **przykładowej aplikacji klienta usługi IoTHub uruchomionej** na symulowanym urządzeniu usługi wprowadź identyfikator urządzenia IoT, do którego chcesz wysłać wiadomość. 

3. Napisz wiadomość w postaci zwykłego tekstu, a następnie kliknij przycisk **Wyślij**.

    Kilka akcji na dzieje się zaraz po kliknięciu przycisku Wyślij. Przykład usługi wysyła komunikat do centrum IoT hub, do którego aplikacja ma dostęp ze względu na podany ciąg połączenia usługi. Centrum IoT sprawdza identyfikator urządzenia, wysyła wiadomość do urządzenia docelowego i wysyła potwierdzenie do urządzenia źródłowego. Aplikacja uruchomiona na symulowanym urządzeniu IoT sprawdza komunikaty z usługi IoT Hub i drukuje tekst z najnowszego tekstu na ekranie.

    Dane wyjściowe powinny wyglądać następująco:

   ![Wyświetlanie komunikatów z chmury do urządzenia](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zobaczyć przykłady kompletnych rozwiązań end-to-end, które używają Usługi IoT Hub, zobacz dokumentację [akceleratorów rozwiązań Azure IoT.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
