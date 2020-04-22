---
title: Wysyłanie wiadomości do serwera MQTT przy użyciu biblioteki klienta usługi Azure MQTT
description: Dowiedz się, jak używać biblioteki klienta MQTT do wysyłania wiadomości do brokera MQTT. Dowiedz się również, jak skonfigurować program mXChip IoT DevKit jako klienta MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 600e64ef5bc3329f0116359066bdcdaf42c13e2e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733488"
---
# <a name="send-messages-to-an-mqtt-server"></a>Wysyłanie wiadomości do serwera MQTT

Systemy Internetu rzeczy (IoT) często mają do czynienia z przerywanymi, niską jakością lub powolnymi połączeniami internetowymi. MQTT to protokół łączności maszyna-maszyna (M2M), który został opracowany z myślą o takich wyzwaniach. 

Biblioteka klienta MQTT używana w tym miejscu jest częścią projektu [Eclipse Paho,](https://www.eclipse.org/paho/) który udostępnia interfejsy API do korzystania z MQTT za pomocą wielu środków transportu.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie uczysz się:
- Jak używać biblioteki klienta MQTT do wysyłania wiadomości do brokera MQTT.
- Jak skonfigurować MXChip Iot DevKit jako klienta MQTT.

## <a name="what-you-need"></a>Co jest potrzebne

Ukończ [przewodnik Wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Podłącz devkit do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

## <a name="open-the-project-folder"></a>Otwieranie folderu projektu

1. Jeśli DevKit jest już połączona z komputerem, odłącz go.

2. Uruchom program VS Code.

3. Podłącz devkit do komputera.

## <a name="open-the-mqttclient-sample"></a>Otwórz próbkę MQTTClient

Rozwiń sekcję **ARDUINO EXAMPLES** po lewej stronie, przejdź do **przykładów dla MXCHIP AZ3166 > MQTT**i wybierz **MQTTClient**. Zostanie otwarte nowe okno kodu programu VS z folderem projektu.

> [!NOTE]
> Można również otworzyć przykład z palety poleceń. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Zbuduj i prześlij szkic Arduino do DevKit

Wpisz `Ctrl+P` (macOS: `Cmd+P`), `task device-upload`aby uruchomić . Po zakończeniu przekazywania DevKit uruchamia ponownie i uruchamia szkic.

![przesyłanie urządzenia](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Może pojawić się komunikat o błędzie "Błąd: AZ3166: Nieznany pakiet". Ten błąd występuje, gdy indeks pakietu płyty nie jest odświeżany poprawnie. Aby rozwiązać ten błąd, zapoznaj się [z sekcją rozwoju często zadawanych pytań ioT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testowanie projektu

W programie VS Code wykonaj tę procedurę, aby otworzyć i skonfigurować Monitor szeregowy:

1. Kliknij `COM[X]` wyraz na pasku stanu, aby `STMicroelectronics`ustawić ![odpowiedni port COM za pomocą: set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kliknij ikonę wtyczki zasilania na pasku ![stanu, aby otworzyć monitor szeregowy: monitor szeregowy](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na pasku stanu kliknij liczbę reprezentującą szybkość transmisji `115200`i ![ustaw ją na: set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Monitor szeregowy wyświetla wszystkie komunikaty wysyłane przez przykładowy szkic. Szkic łączy DevKit z siecią Wi-Fi. Po pomyślnym zakończeniu połączenia Wi-Fi szkic wysyła wiadomość do brokera MQTT. Następnie próbka wielokrotnie wysyła dwa komunikaty "iot.eclipse.org" przy użyciu odpowiednio QoS 0 i QoS 1.

![wyjście szeregowe](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z [często zadawanymi pytaniami dotyczącymi programu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub połącz się z następującymi kanałami:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Zobacz też

* [Łączenie zestawu IoT DevKit AZ3166 z centrum Usługi Azure IoT Hub w chmurze](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Wstrząsnąć, Wstrząsnąć na Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy nauczyłeś się, jak skonfigurować MXChip Iot DevKit jako klienta MQTT i użyć biblioteki klienta MQTT do wysyłania wiadomości do brokera MQTT, oto sugerowane następne kroki:

* [Omówienie akceleratora rozwiązań do zdalnego monitorowania usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
