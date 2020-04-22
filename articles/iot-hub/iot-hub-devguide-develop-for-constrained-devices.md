---
title: Tworzenie usługi Azure IoT Hub dla urządzeń o ograniczonym zasięgu przy użyciu zestawu SDK C usługi IoT Hub
description: Przewodnik dla deweloperów — wskazówki dotyczące tworzenia przy użyciu zestawów SDK usługi Azure IoT dla urządzeń z ograniczeniami.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9010ff582f05e81e17e280e20f180ceccf0e746f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733199"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Tworzenie urządzeń o ograniczonym zasięgu przy użyciu zestawu SDK usługi Azure IoT C

Zestaw SDK usługi Azure IoT Hub C jest napisany w języku ANSI C (C99), co sprawia, że doskonale nadaje się do obsługi różnych platform z małymi rozmiarami dysku i pamięci. Zalecana pamięć RAM wynosi co najmniej 64 KB, ale dokładna ilość pamięci zależy od używanego protokołu, liczby otwartych połączeń, a także od docelowej platformy.
> [!NOTE]
> * Zestaw SDK usługi Azure IoT C regularnie publikuje informacje o zużyciu zasobów, aby ułatwić tworzenie.  Odwiedź nasze [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) i zapoznaj się z najnowszym punktem odniesienia.
>

C SDK jest dostępny w formie pakietu z apt-get, NuGet i MBED. Aby kierować ograniczone urządzenia, można utworzyć sdk lokalnie dla platformy docelowej. W tej dokumentacji pokazano, jak usunąć niektóre funkcje, aby zmniejszyć rozmiar zestawu C SDK za pomocą [cmake](https://cmake.org/). Ponadto w tej dokumentacji omówiono modele programowania najlepszych praktyk do pracy z urządzeniami z ograniczeniami.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Tworzenie C SDK dla urządzeń z ograniczeniami

Tworzenie sdk C dla urządzeń z ograniczeniami.

### <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z tym [przewodnikiem konfiguracji SDK języka C,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) aby przygotować środowisko programistyczne do tworzenia sdk C. Zanim dojdziesz do kroku do budowania z cmake, można wywołać flagi cmake, aby usunąć nieużywane funkcje.

### <a name="remove-additional-protocol-libraries"></a>Usuwanie dodatkowych bibliotek protokołów

C SDK obsługuje dziś pięć protokołów: MQTT, MQTT przez WebSocket, AMQPs, AMQP przez WebSocket i HTTPS. Większość scenariuszy wymaga od jednego do dwóch protokołów uruchomionych na kliencie, w związku z tym można usunąć bibliotekę protokołów, której nie używasz z sdk. Dodatkowe informacje dotyczące wybierania odpowiedniego protokołu komunikacyjnego dla danego scenariusza można znaleźć w [1999](iot-hub-devguide-protocols.md)r. Wybierz protokół komunikacyjny usługi IoT Hub . Na przykład MQTT jest lekki protokół, który jest często lepiej nadaje się do urządzeń ograniczonych.

Biblioteki AMQP i HTTP można usunąć za pomocą następującego polecenia cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Usuwanie możliwości rejestrowania sdk

C SDK zapewnia obszerne rejestrowanie w całej pomocy w debugowaniu. Funkcję rejestrowania urządzeń produkcyjnych można usunąć za pomocą następującego polecenia cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Usuwanie funkcji przekazywania do obiektu blob

Duże pliki można przekazać do usługi Azure Storage przy użyciu wbudowanej funkcji w sdk. Usługa Azure IoT Hub działa jako dyspozytor do skojarzonego konta usługi Azure Storage. Za pomocą tej funkcji można wysyłać pliki multimedialne, duże partie telemetryczne i dzienniki. Więcej informacji można uzyskać [podczas przesyłania plików za pomocą usługi IoT Hub](iot-hub-devguide-file-upload.md). Jeśli aplikacja nie wymaga tej funkcji, można usunąć tę funkcję za pomocą następującego polecenia cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Pasek do pracy w środowisku Linuksa

Jeśli pliki binarne są uruchamiane w systemie Linux, można wykorzystać [polecenie strip,](https://en.wikipedia.org/wiki/Strip_(Unix)) aby zmniejszyć rozmiar aplikacji końcowej po kompilacji.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modele programowania dla urządzeń o ograniczonym dostępie

Następnie przyjrzyj się modelom programowania dla urządzeń o ograniczonym dostępie.

### <a name="avoid-using-the-serializer"></a>Unikaj używania serializatora

Zestaw C SDK ma opcjonalny [serializator SDK C,](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)który umożliwia używanie tabel mapowania deklaratywnego do definiowania metod i właściwości bliźniaczej reprezentacji urządzenia. Serializator został zaprojektowany w celu uproszczenia rozwoju, ale dodaje obciążenie, które nie jest optymalne dla urządzeń o ograniczonym dostępie. W takim przypadku należy rozważyć użycie pierwotnych interfejsów API klienta i przeanalizować JSON przy użyciu lekkiego analizatora, takiego jak [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Użyj dolnej warstwy (_LL_)

Zestaw C SDK obsługuje dwa modele programowania. Jeden zestaw ma interfejsy API z poprawką _LL,_ co oznacza niższą warstwę. Ten zestaw interfejsów API jest lżejszy i nie rozkręca wątków roboczych, co oznacza, że użytkownik musi ręcznie kontrolować planowanie. Na przykład dla klienta urządzenia interfejsy API _LL_ można znaleźć w tym [pliku nagłówka](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Inny zestaw interfejsów API bez indeksu _LL_ jest nazywany warstwą wygody, gdzie wątek roboczy jest obracany automatycznie. Na przykład interfejsy API warstwy wygody dla klienta urządzenia można znaleźć w tym [pliku nagłówkowym klienta urządzenia IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Dla urządzeń o ograniczonym, gdzie każdy dodatkowy wątek może zająć znaczny procent zasobów systemowych, należy rozważyć użycie interfejsów API _LL._

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o architekturze SDK języka C usługi Azure IoT:
-    [Kod źródłowy SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c/)
-    [Wprowadzenie zestawu SDK urządzenia Usługi Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md)
