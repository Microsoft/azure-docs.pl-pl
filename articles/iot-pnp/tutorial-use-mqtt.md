---
title: Samouczek — używanie MQTT do tworzenia klienta urządzenia usługi Azure IoT Plug and Play | Microsoft Docs
description: Samouczek — bezpośrednie używanie protokołu MQTT do tworzenia klienta urządzenia IoT Plug and Play bez używania zestawów SDK urządzeń usługi Azure IoT
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 64ff4615c2b41e341352dce5143d48ec8e6e802a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066776"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Samouczek — korzystanie z programu MQTT do opracowania klienta urządzenia Plug and Play IoT

Jeśli to możliwe, należy użyć jednego z zestawów SDK urządzeń usługi Azure IoT do kompilowania klientów urządzeń Plug and Play IoT. Jednak w scenariuszach, takich jak urządzenie z ograniczoną ilością pamięci, może być konieczne użycie biblioteki MQTT do komunikowania się z Centrum IoT Hub.

Przykład w tym samouczku używa biblioteki [Mosquitto](http://mosquitto.org/) MQTT i programu Visual Studio. W krokach w tym samouczku przyjęto założenie, że używasz systemu Windows na komputerze deweloperskim.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobierz i skompiluj bibliotekę Mosquitto.
> * Zmodyfikuj przykładowy kod MQTT oparty na języku C, aby udostępnić urządzenie Plug and Play IoT.
> * Zidentyfikuj tematy MQTT używane przez urządzenie IoT Plug and Play.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten samouczek w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas [instalowania](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) programu Visual Studio dołączysz **programowanie klasyczne przy użyciu języka C++**
* [Usługa Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Za pomocą narzędzia *Azure IoT Explorer* Dodaj nowe urządzenie do IoT Hub. Po zakończeniu [konfigurowania środowiska dla przewodników Szybki Start i samouczków Plug and Play IoT](set-up-environment.md)została skonfigurowana usługa IoT Hub i narzędzie Azure IoT Explorer:

1. Uruchom narzędzie **Azure IoT Explorer** .
1. Na stronie **centra IoT** wybierz pozycję **Wyświetl urządzenia w tym centrum**.
1. Na stronie **urządzenia** wybierz pozycję **+ Nowy**.
1. Utwórz urządzenie o nazwie *My-MQTT-Device* , które używa automatycznie generowanego klucza symetrycznego.
1. Na stronie **tożsamość urządzenia** rozwiń węzeł **Parametry połączenia z tokenem SAS**.
1. Wybierz **klucz podstawowy** , który ma być używany jako **klucz symetryczny**, ustaw czas wygaśnięcia na 60 minut, a następnie wybierz pozycję **Generuj**.
1. Skopiuj wygenerowane **Parametry połączenia tokenu sygnatury dostępu współdzielonego**, Użyj tej wartości w dalszej części tego samouczka.

## <a name="clone-sample-repo"></a>Klonowanie przykładowego repozytorium

Użyj następującego polecenia, aby sklonować przykładowe repozytorium do odpowiedniej lokalizacji na komputerze lokalnym:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Zainstaluj bibliotekę MQTT

Użyj `vcpkg` Narzędzia, aby pobrać i skompilować bibliotekę Mosquitto.

Użyj następującego polecenia, aby sklonować repozytorium **Vcpkg** do odpowiedniej lokalizacji na komputerze lokalnym:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Aby przygotować środowisko, użyj następujących poleceń `vcpkg` . Po uruchomieniu programu jest potrzebny wiersz polecenia z podwyższonym poziomem uprawnień `vcpkg integrate install` :

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Użyj następującego polecenia, aby pobrać i skompilować bibliotekę Mosquitto/zaćmienie:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrowanie przykładu do Plug and Play IoT

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Przeglądanie przykładowego kodu Plug and Play innego niż IoT

Przed skompilowaniem i uruchomieniem programu zaktualizuj kod ze szczegółowymi informacjami o Centrum IoT Hub i urządzeniu.

Aby wyświetlić przykładowy kod w programie Visual Studio, Otwórz plik rozwiązania *MQTTWin32. sln* w folderze *IoTMQTTSample\src\Windows* .

W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **TelemetryMQTTWin32** i wybierz pozycję **Ustaw jako projekt startowy**.

W projekcie **TelemetryMQTTWin32** Otwórz plik źródłowy **MQTT_Mosquitto. cpp** . Zaktualizuj definicje informacji o połączeniu z informacjami o urządzeniu, które zostały wcześniej wykonane. Zastąp symbole zastępcze ciągu tokenu dla:

* `IOTHUBNAME` Identyfikator o nazwie Centrum IoT.
* `DEVICEID` Identyfikator przy użyciu `my-mqtt-device` .
* `PWD` Identyfikator z poprawną częścią parametrów połączenia tokenu sygnatury dostępu współdzielonego, które zostały wygenerowane dla urządzenia. Użyj części parametrów połączenia od do `SharedAccessSignature sr=` końca.

Sprawdź, czy kod działa prawidłowo, uruchamiając program Azure IoT Explorer, Rozpocznij nasłuchiwanie telemetrii.

Uruchom aplikację (Ctrl + F5), po kilku sekundach zobaczysz dane wyjściowe, które wyglądają następująco:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Dane wyjściowe z przykładowej aplikacji MQTT":::

W programie Azure IoT Explorer można zobaczyć, że urządzenie nie jest urządzeniem Plug and Play IoT:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Urządzenie Plug and Play inne niż IoT w programie Azure IoT Explorer":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Uczyń urządzenie urządzeniem Plug and Play IoT

Urządzenia IoT Plug and Play muszą być zgodne z zestawem prostych Konwencji. Jeśli urządzenie wyśle Identyfikator modelu podczas nawiązywania połączenia, zostaje ono urządzeniem Plug and Play IoT.

W tym przykładzie należy dodać Identyfikator modelu do pakietu połączenia MQTT. Identyfikator modelu można przekazać jako parametr QueryString w `USERNAME` i zmienić `api-version` na `2020-09-30` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Skompiluj ponownie i uruchom przykład.

Sznurki urządzenia zawiera teraz Identyfikator modelu:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Wyświetlanie identyfikatora modelu w programie Azure IoT Explorer":::

Teraz można nawigować po składniku Plug and Play IoT:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Wyświetlanie składników w programie Azure IoT Explorer":::

Teraz można zmodyfikować kod urządzenia, aby zaimplementować dane telemetryczne, właściwości i polecenia zdefiniowane w modelu. Aby zapoznać się z przykładową implementacją termostatu urządzenia za pomocą biblioteki Mosquitto, zobacz [Używanie MQTT PnP z platformą Azure IoTHub bez zestawu IoT SDK w systemie Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) w witrynie GitHub.

> [!NOTE]
>Klient używa `IoTHubRootCA_Baltimore.pem` pliku certyfikatu głównego w celu zweryfikowania tożsamości Centrum IoT, z którym jest nawiązywane połączenie.

### <a name="mqtt-topics"></a>Tematy MQTT

Poniższe definicje dotyczą tematów MQTT, których urządzenie używa do wysyłania informacji do centrum IoT. Aby dowiedzieć się więcej, zobacz temat [komunikacja z Centrum IoT Hub przy użyciu protokołu MQTT](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE`Definiuje temat, którego używa urządzenie do raportowania interfejsów, które implementuje.
* `DEVICETWIN_PATCH_MESSAGE`Definiuje temat, którego używa urządzenie do raportowania aktualizacji właściwości do centrum IoT Hub.
* `DEVICE_TELEMETRY_MESSAGE`Definiuje temat używany przez urządzenie do wysyłania telemetrii do centrum IoT Hub.

Aby uzyskać więcej informacji na temat MQTT, odwiedź [przykłady MQTT w repozytorium GitHub usługi Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób modyfikowania klienta urządzenia MQTT w celu przestrzegania Konwencji Plug and Play IoT. Aby dowiedzieć się więcej o obsłudze IoT Hub dla protokołu MQTT, zobacz:

> [!div class="nextstepaction"]
> [Komunikacja z Centrum IoT Hub przy użyciu protokołu MQTT](../iot-hub/iot-hub-mqtt-support.md)