---
title: Tworzenie aplikacji dla urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT | Microsoft Docs
description: Przewodnik dla deweloperów — informacje na temat tworzenia aplikacji dla urządzeń przenośnych przy użyciu zestawów SDK platformy Azure IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 63fade17d10189cbe8e775b2a859902f1c2bb004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151638"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Tworzenie aplikacji dla urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT

Elementy w Internet rzeczy mogą odnosić się do szerokiego zakresu urządzeń z różnymi możliwościami: czujników, mikrokontrolerów, urządzeń inteligentnych, bram przemysłowych, a nawet urządzeń przenośnych.  Urządzenie przenośne może być urządzeniem IoT, które wysyła dane telemetryczne z urządzenia do chmury i zarządzane przez chmurę.  Może to być również urządzenie z uruchomioną aplikacją usługi zaplecza, która zarządza innymi urządzeniami IoT.  W obu przypadkach [usługi Azure IoT Hub SDK](./iot-hub-devguide-sdks.md) mogą służyć do tworzenia aplikacji, które działają dla urządzeń przenośnych.  

## <a name="develop-for-native-ios-platform"></a>Programowanie dla natywnej platformy systemu iOS

Usługa Azure IoT Hub SDK zapewnia natywną obsługę platformy iOS za pomocą zestawu Azure IoT Hub C SDK.  Można go traktować jako zestaw SDK systemu iOS, który można uwzględnić w projekcie SWIFT lub XCode.  Istnieją dwa sposoby używania zestawu SDK języka C w systemie iOS:

* Bezpośrednio używaj bibliotek CocoaPod w projekcie XCode.  
* Pobierz kod źródłowy dla zestawu C SDK i skompiluj dla platformy iOS, postępując zgodnie z [instrukcją kompilacji](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) dla MacOS.  

Usługa Azure IoT Hub C SDK jest zapisywana w C99 w celu uzyskania maksymalnej przenośności do różnych platform.  Proces przenoszenia polega na zapisaniu warstwy wdrażania elastycznego dla składników specyficznych dla platformy, które można znaleźć w tym miejscu dla [systemu iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funkcje zestawu SDK języka C mogą być wykorzystywane na platformie iOS, w tym obsługiwane elementy podstawowe platformy Azure IoT Hub i funkcje specyficzne dla zestawu SDK, takie jak zasady ponawiania dla niezawodności sieci.  Interfejs dla zestawu iOS SDK jest również podobny do interfejsu dla zestawu Azure IoT Hub C SDK.  

Te dokumenty przedstawiają sposób tworzenia aplikacji na urządzeniu lub aplikacji usługi na urządzeniu z systemem iOS:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-ios.md)  
* [Wysyłanie komunikatów z chmury do urządzenia za pomocą usługi IoT Hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Tworzenie za pomocą bibliotek CocoaPod systemu IoT Hub Azure

Zestawy SDK platformy Azure IoT Hub udostępniają zestaw CocoaPodch dla projektowania dla systemu iOS.  Aby wyświetlić najnowszą listę bibliotek CocoaPod, zobacz [CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Gdy odpowiednie biblioteki zostaną dołączone do projektu XCode, istnieją dwa sposoby zapisu IoT Hub pokrewnego kodu:

* Funkcja języka C: Jeśli projekt jest zapisywana w celu języka C, można bezpośrednio wywoływać interfejsy API z usługi Azure IoT Hub C SDK.  Jeśli projekt jest napisany w postaci SWIFT, możesz wywołać `@objc func` przed utworzeniem funkcji i dopisać wszystkie logike związane z platformą Azure IoT Hub przy użyciu kodu c lub Project-c.  Zestaw przykładów demonstrujących oba te elementy można znaleźć w [przykładowym repozytorium](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Uwzględnienie przykładów C: Jeśli Zapisano aplikację urządzenia C, można odwołać się do niej bezpośrednio w projekcie XCode:
    * Dodaj plik Sample. c do projektu XCode z XCode.  
    * Dodaj plik nagłówka do zależności.  Plik nagłówkowy jest zawarty w przykładowym [repozytorium](https://github.com/Azure-Samples/azure-iot-samples-ios) . Aby uzyskać więcej informacji, odwiedź stronę dokumentacji firmy Apple na potrzeby języka [C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Programowanie dla platformy Android
Zestaw Azure IoT Hub Java SDK obsługuje platformę Android.  W przypadku przetestowanej wersji interfejsu API odwiedź naszą [stronę pomocy technicznej](iot-hub-device-sdk-platform-support.md) , aby uzyskać najnowszą aktualizację.

Te dokumenty przedstawiają sposób opracowywania aplikacji lub aplikacji na urządzeniu z systemem Android przy użyciu usług Gradle i Android Studio:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-android.md)  
* [Szybki Start: sterowanie urządzeniem](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja interfejsu API REST IoT Hub](/rest/api/iothub/)
* [Kod źródłowy zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c)