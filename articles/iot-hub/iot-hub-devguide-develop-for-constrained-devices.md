---
title: Platforma Azure IoT Hub opracowywanie w przypadku urządzeń z ograniczeniami przy użyciu zestawu SDK IoT Hub C
description: Przewodnik dla deweloperów — wskazówki dotyczące sposobu tworzenia aplikacji przy użyciu zestawów SDK usługi Azure IoT dla urządzeń z ograniczeniami.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81733199"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Programowanie dla urządzeń z ograniczeniami przy użyciu zestawu SDK języka C usługi Azure IoT

Usługa Azure IoT Hub C SDK jest zapisywana w języku ANSI C (C99), dzięki czemu można korzystać z różnych platform z małym dyskiem i ilością pamięci. Zalecana ilość pamięci RAM to co najmniej 64 KB, ale dokładna ilość pamięci zależy od używanego protokołu, liczby otwartych połączeń, a także do lokalizacji platformy.
> [!NOTE]
> * Zestaw SDK języka C usługi Azure IoT regularnie publikuje informacje o zużyciu zasobów, aby ułatwić programowanie.  Odwiedź nasze [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) i zapoznaj się z najnowszym wzorcem.
>

Zestaw C SDK jest dostępny w formie pakietu z apt-get, NuGet i MBED. W celu przekierowania urządzeń z ograniczeniami możesz chcieć lokalnie skompilować zestaw SDK dla platformy docelowej. W tej dokumentacji pokazano, jak usunąć niektóre funkcje, aby zmniejszyć rozmiary zestawu C SDK przy użyciu [CMAKE](https://cmake.org/). Ponadto ta dokumentacja omawia modele programowania najlepszych rozwiązań do pracy z ograniczonymi urządzeniami.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Kompilowanie zestawu SDK języka C dla urządzeń z ograniczeniami

Tworzenie zestawu SDK języka C dla urządzeń z ograniczeniami.

### <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z tym [przewodnikiem Instalatora zestawu c SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) , aby przygotować środowisko programistyczne do tworzenia zestawu SDK języka c. Przed przejściem do kroku kompilowania za pomocą CMAKE można wywołać flagi CMAKE w celu usunięcia nieużywanych funkcji.

### <a name="remove-additional-protocol-libraries"></a>Usuwanie dodatkowych bibliotek protokołów

Zestaw SDK języka C obsługuje pięć protokołów Dzisiaj: MQTT, MQTT za pośrednictwem protokołu WebSocket, AMQPs, AMQP przy użyciu protokołu WebSocket i protokołu HTTPS. Większość scenariuszy wymaga jednego do dwóch protokołów uruchomionych na kliencie, dlatego można usunąć bibliotekę protokołów, która nie jest używana z zestawu SDK. Dodatkowe informacje na temat wybierania odpowiedniego protokołu komunikacyjnego dla danego scenariusza można znaleźć w temacie [Wybieranie protokołu komunikacyjnego IoT Hub](iot-hub-devguide-protocols.md). Na przykład MQTT jest lekkim protokołem, który jest często lepszym rozwiązaniem w przypadku urządzeń z ograniczeniami.

Biblioteki AMQP i HTTP można usunąć za pomocą następującego polecenia CMAKE:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Usuwanie możliwości rejestrowania zestawu SDK

Zestaw C SDK zapewnia obszerne rejestrowanie w celu ułatwienia debugowania. Możesz usunąć funkcję rejestrowania dla urządzeń produkcyjnych przy użyciu następującego polecenia CMAKE:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Usuń funkcję przekazywania do obiektu BLOB

Możesz przekazać duże pliki do usługi Azure Storage, korzystając z wbudowanej funkcji w zestawie SDK. Usługa Azure IoT Hub pełni rolę dyspozytora do skojarzonego konta usługi Azure Storage. Ta funkcja służy do wysyłania plików multimedialnych, dużych partii danych telemetrycznych i dzienników. Więcej informacji na temat [przekazywania plików](iot-hub-devguide-file-upload.md)można znaleźć w IoT Hub. Jeśli aplikacja nie wymaga tej funkcji, możesz usunąć tę funkcję przy użyciu następującego polecenia CMAKE:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Uruchamianie paska w środowisku systemu Linux

Jeśli dane binarne są uruchamiane w systemie Linux, można użyć [paska polecenia](https://en.wikipedia.org/wiki/Strip_(Unix)) , aby zmniejszyć rozmiar aplikacji końcowej po kompilacji.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modele programowania dla urządzeń z ograniczeniami

Następnie zapoznaj się z modelami programowania dla urządzeń z ograniczeniami.

### <a name="avoid-using-the-serializer"></a>Unikaj korzystania z serializatora

Zestaw C SDK zawiera opcjonalny [serializator zestawu SDK języka c](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), który umożliwia Definiowanie metod i właściwości bliźniaczych urządzeń przy użyciu tabel mapowania deklaratywnego. Serializator zaprojektowano w celu uproszczenia programowania, ale dodaje narzuty, co nie jest optymalne w przypadku urządzeń z ograniczeniami. W takim przypadku należy rozważyć użycie podstawowych interfejsów API klienta i przeanalizować kod JSON przy użyciu uproszczonego parsera, takiego jak [Parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Użyj dolnej warstwy (_wszystkie_)

Zestaw SDK języka C obsługuje dwa modele programowania. Jeden zestaw ma interfejsy API z wrostkoweami _ll_ , co oznacza niższą warstwę. Ten zestaw interfejsów API jest jaśniejszy i nie można uruchomić wątków roboczych, co oznacza, że użytkownik musi ręcznie kontrolować planowanie. Na przykład dla klienta urządzenia interfejsy API _szystkie_ można znaleźć w tym [pliku nagłówkowym](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Inny zestaw interfejsów API _bez indeksu są_ nazywane warstwą wygodną, gdzie wątek roboczy jest automatycznie Spuninst. Na przykład wygodne interfejsy API dla klienta urządzenia znajdują się w tym [pliku nagłówkowym klienta urządzenia IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). W przypadku urządzeń z ograniczeniami, w których każdy dodatkowy wątek może uzyskać znaczną część zasobów systemowych, należy rozważyć użycie interfejsów API _ll_ .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o architekturze zestawu SDK języka C dla usługi Azure IoT:
-    [Kod źródłowy zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/)
-    [Wprowadzenie do zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md)
