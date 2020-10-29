---
title: Komunikaty z chmury do urządzeń za pomocą usługi Azure IoT Hub (iOS) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla systemu iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: 15c0df33b8f09ec71f2be913d72f0785dc766375
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027537"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-ios.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.

* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.

* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia ( *Opinie* ) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzeń można znaleźć w sekcji komunikaty w [przewodniku dewelopera IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego artykułu są uruchamiane dwa szybkie projekty systemu iOS:

* **przykład — urządzenie** , ta sama aplikacja utworzona w [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-ios.md), która łączy się z Centrum IoT Hub i odbiera komunikaty z chmury do urządzenia.

* **Przykładowa usługa** , która wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym C, Java, Python i JavaScript) za pomocą zestawów SDK urządzeń usługi Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z tym kodem samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Aktywna usługa IoT Hub na platformie Azure.

* Przykład kodu z [przykładów platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Najnowsza wersja środowiska [XCode](https://developer.apple.com/xcode/) korzystająca z najnowszej wersji zestawu SDK systemu iOS. Ten przewodnik Szybki start przetestowano przy użyciu środowiska XCode 9.3 i systemu iOS 11.3.

* Najnowsza wersja menedżera [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Symulowanie urządzenia IoT

W tej sekcji Symulowano urządzenie z systemem iOS z uruchomioną aplikacją, która umożliwia odbieranie komunikatów z chmury do urządzeń z Centrum IoT. 

Jest to przykładowe urządzenie utworzone w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-ios.md). Jeśli masz już uruchomiony program, możesz pominąć tę sekcję.

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

W oknie terminalu przejdź do folderu Azure-IoT-Samples-iOS pobranego w ramach wymagań wstępnych. Następnie przejdź do przykładowego projektu:

```sh
cd quickstart/sample-device
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile** :

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności.

### <a name="run-the-sample-device-application"></a>Uruchamianie przykładowej aplikacji urządzenia

1. Pobierz parametry połączenia dla urządzenia. Możesz skopiować ten ciąg z [Azure Portal](https://portal.azure.com) w bloku szczegóły urządzenia lub pobrać go przy użyciu poniższego polecenia CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń **przykładowy projekt MQTT klienta** , a następnie folder o tej samej nazwie.  

3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 

4. Wyszukaj zmienną **ConnectionString** i zaktualizuj wartość przy użyciu parametrów połączenia urządzenia, które zostały skopiowane w pierwszym kroku.

5. Zapisz zmiany. 

6. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Skompiluj i uruchom** lub kombinacji klawiszy **Command + R** .

   ![Zrzut ekranu przedstawia przycisk Kompiluj i uruchom w emulatorze urządzenia.](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-ios.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Symulowanie urządzenia usługi

W tej sekcji Zasymulowano drugie urządzenie z systemem iOS za pomocą aplikacji SWIFT, która wysyła komunikaty z chmury do urządzenia za pomocą usługi IoT Hub. Ta konfiguracja jest przydatna w scenariuszach IoT, w których jeden iPhone lub tablet iPad działa jako kontroler dla innych urządzeń z systemem iOS podłączonych do centrum IoT Hub.

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

Przejdź do folderu przykładów usługi Azure IoT dla systemu iOS pobranego w sekcji wymagania wstępne. Następnie przejdź do przykładowego projektu usługi:

```sh
cd quickstart/sample-service
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile** :

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności.

### <a name="run-the-sample-service-application"></a>Uruchom przykładową aplikację usługi

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Rozwiń projekt **AzureIoTServiceSample** , a następnie rozwiń folder o tej samej nazwie.  

3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 

4. Wyszukaj zmienną **ConnectionString** i zaktualizuj wartość przy użyciu parametrów połączenia usługi, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia z usługą IoT Hub](#get-the-iot-hub-connection-string).

5. Zapisz zmiany.

6. W Xcode Zmień ustawienia emulatora na inne urządzenie z systemem iOS niż używane do uruchamiania urządzenia IoT. XCode nie może uruchomić wielu emulatorów tego samego typu.

   ![Zmień urządzenie emulatora](media/iot-hub-ios-swift-c2d/change-device.png)

7. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Kompiluj i Uruchom, a** następnie polecenie Key kombi **+ r** .

   ![Zrzut ekranu przedstawia przycisk Kompilacja i uruchomienie.](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

Teraz można przystąpić do korzystania z dwóch aplikacji do wysyłania i odbierania komunikatów z chmury do urządzenia.

1. W **przykładowej aplikacji dla systemu iOS** działającej na symulowanym urządzeniu IoT kliknij przycisk **Uruchom** . Aplikacja uruchamia wysyłanie komunikatów z urządzenia do chmury, ale również zaczyna nasłuchuje komunikatów z chmury do urządzenia.

   ![Wyświetlanie przykładowej aplikacji urządzenia IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. W **przykładowej aplikacji klienta usługi IoTHub** działającej na urządzeniu symulowanej usługi wprowadź identyfikator urządzenia IoT, do którego chcesz wysłać wiadomość. 

3. Napisz wiadomość w postaci zwykłego tekstu, a następnie kliknij przycisk **Wyślij** .

    Kilka akcji odbywa się zaraz po kliknięciu przycisku Wyślij. Przykład usługi wysyła wiadomość do centrum IoT Hub, do którego aplikacja ma dostęp z powodu dostarczonych przez Ciebie parametrów połączenia z usługą. Centrum IoT sprawdza identyfikator urządzenia, wysyła komunikat do urządzenia docelowego i wysyła potwierdzenie potwierdzenia do urządzenia źródłowego. Aplikacja uruchomiona na symulowanym urządzeniu IoT sprawdza komunikaty z IoT Hub i drukuje tekst z ostatniego pliku na ekranie.

    Dane wyjściowe powinny wyglądać podobnie do następującego przykładu:

   ![Wyświetlanie komunikatów z chmury do urządzenia](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań, które używają IoT Hub, zobacz dokumentację [akceleratorów rozwiązań usługi Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/) .

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).
