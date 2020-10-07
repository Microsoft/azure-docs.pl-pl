---
title: Opracowywanie urządzeń na platformie Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie tworzenia urządzeń w celu nawiązania połączenia z aplikacją IoT Central. Urządzenia używają telemetrii do wysyłania danych i właściwości przesyłania strumieniowego w celu zgłoszenia stanu urządzenia. W usłudze IoT Central można ustawić stan urządzenia przy użyciu właściwości z możliwością zapisu i wywołań poleceń na urządzeniu.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 39ce436cd59447b2b6f8d9f88deaab80b00dd639
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812356"
---
# <a name="iot-central-device-development-overview"></a>Omówienie tworzenia urządzenia usługi IoT Central

*Ten artykuł ma zastosowanie do deweloperów urządzeń.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przegląd jest przeznaczony dla deweloperów urządzeń, którzy implementują kod do uruchamiania na urządzeniach, które łączą się z IoT Central.

Urządzenia współpracują z aplikacją IoT Central przy użyciu następujących elementów podstawowych:

- _Telemetrię_ to dane wysyłane przez urządzenie do IoT Central. Na przykład strumień wartości temperatury z czujnika dołączania.
- _Właściwości_ to wartości stanu, które urządzenie ma zgłaszać IoT Central. Na przykład bieżąca wersja oprogramowania układowego urządzenia. Możesz również mieć zapisywalne właściwości, które IoT Central mogą być aktualizowane na urządzeniu, takie jak temperatura docelowa.
- _Polecenia_ są wywoływane z IoT Central w celu sterowania zachowaniem urządzenia. Na przykład aplikacja IoT Central może wywołać polecenie w celu ponownego uruchomienia urządzenia.

Konstruktor rozwiązań jest odpowiedzialny za konfigurowanie pulpitów nawigacyjnych i widoków w interfejsie użytkownika IoT Central sieci Web w celu wizualizowania danych telemetrycznych, zarządzania właściwościami i wywoływania poleceń.

## <a name="types-of-device"></a>Typy urządzeń

W poniższych sekcjach opisano główne typy urządzeń, które można połączyć z aplikacją IoT Central:

### <a name="standalone-device"></a>Urządzenie autonomiczne

Urządzenie autonomiczne łączy się bezpośrednio z IoT Central. Urządzenie autonomiczne zwykle wysyła dane telemetryczne ze swoich dołączanych lub połączonych czujników do aplikacji IoT Central. Urządzenia autonomiczne mogą również raportować wartości właściwości, odbierać wartości właściwości zapisu i odpowiadać na polecenia.

### <a name="gateway-device"></a>Urządzenie bramy

Urządzenie bramy zarządza jednym lub wieloma urządzeniami podrzędnymi, które łączą się z aplikacją IoT Central. Użyj IoT Central, aby skonfigurować relacje między urządzeniami podrzędnymi a urządzeniem bramy. Aby dowiedzieć się więcej, zobacz [Definiowanie nowego typu urządzenia bramy IoT w aplikacji IoT Central platformy Azure](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Urządzenie brzegowe

Urządzenie brzegowe łączy się bezpośrednio z IoT Central, ale działa jako pośrednik dla innych urządzeń znanych jako _urządzenia liścia_. Urządzenie brzegowe zwykle znajduje się w pobliżu urządzeń liściowych, na których działa jako pośrednik. Scenariusze korzystające z urządzeń brzegowych obejmują:

- Włącz urządzenia, które nie mogą łączyć się bezpośrednio z IoT Central, aby nawiązać połączenie za pomocą urządzenia brzegowego. Na przykład urządzenie liścia może używać połączenia Bluetooth do łączenia się z urządzeniem brzegowym, które następnie łączy się za pośrednictwem Internetu w celu IoT Central.
- Agreguj dane telemetryczne przed ich wysłaniem do IoT Central. Takie podejście może pomóc w zmniejszeniu kosztów wysyłania danych do IoT Central.
- Kontroluj lokalnie urządzenia liściowe, aby uniknąć opóźnień związanych z nawiązywaniem połączenia z IoT Central przez Internet.

Urządzenie brzegowe może również wysyłać własne dane telemetryczne, raportować jego właściwości i odpowiadać na zapisywalne aktualizacje właściwości i polecenia.

IoT Central widzi tylko urządzenie brzegowe, a nie urządzenia liściowe podłączone do urządzenia brzegowego.

Aby dowiedzieć się więcej, zobacz [Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Łączenie urządzenia

Usługa Azure IoT Central używa [usługi azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania wszystkimi rejestracjami i połączeniem urządzeń.

Korzystanie z usługi DPS umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń w odpowiedniej skali.
- Można generować poświadczenia urządzenia i konfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pomocą interfejsu użytkownika IoT Central.
- Aby zarejestrować urządzenia w IoT Central, należy użyć własnych identyfikatorów urządzeń. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden spójny sposób łączenia urządzeń z IoT Central.

Aby dowiedzieć się więcej, zobacz temat [połączono z usługą Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Zabezpieczenia

Połączenie między urządzeniem a aplikacją IoT Central jest zabezpieczone przy użyciu [sygnatur dostępu współdzielonego](./concepts-get-connected.md#connect-devices-at-scale-using-sas) lub [certyfikatów X. 509](./concepts-get-connected.md#connect-devices-using-x509-certificates)standardowych w branży.

### <a name="communication-protocols"></a>Protokoły komunikacyjne

Protokoły komunikacyjne używane przez urządzenie do nawiązywania połączeń IoT Central obejmują MQTT, AMQP i HTTPS. Wewnętrznie, IoT Central używa usługi IoT Hub, aby umożliwić łączność z urządzeniem. Aby uzyskać więcej informacji na temat protokołów komunikacyjnych obsługiwanych przez IoT Hub na potrzeby łączności urządzeń, zobacz [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementowanie urządzenia

Aby zaimplementować zachowanie urządzenia, użyj jednego z [zestawów SDK urządzeń usługi Azure IoT](#languages-and-sdks) . Kod powinien:

- Zarejestruj urządzenie w usłudze DPS i Skorzystaj z informacji z usługi DPS, aby nawiązać połączenie z wewnętrznym centrum IoT Hub w aplikacji IoT Central.
- Wysyłaj dane telemetryczne w formacie, który określa szablon urządzenia w IoT Central. IoT Central używa szablonu urządzenia, aby określić, jak używać telemetrii do wizualizacji i analizy.
- Zsynchronizuj wartości właściwości między urządzeniem a IoT Central. Szablon urządzenia określa nazwy właściwości i typy danych, dzięki czemu IoT Central mogą wyświetlać informacje.
- Zaimplementuj programy obsługi poleceń dla poleceń, które są określone w szablonie urządzenia. Szablon urządzenia określa nazwy poleceń i parametry, które powinny być używane przez urządzenie.

Aby uzyskać więcej informacji na temat roli szablonów urządzeń, zobacz [co to są szablony urządzeń?](./concepts-device-templates.md)

Aby zapoznać się z przykładowym kodem, zobacz [Tworzenie i łączenie aplikacji klienckiej Node.js](./tutorial-connect-device-nodejs.md) lub [Tworzenie i łączenie aplikacji klienckiej języka Python](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Języki i zestawy SDK

Aby uzyskać więcej informacji na temat obsługiwanych języków i zestawów SDK, zobacz [Omówienie i używanie zestawów SDK urządzeń IoT Hub platformy Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia i chcesz szczegółowe do pewnego kodu, sugerowanym następnym krokiem jest [utworzenie i podłączenie aplikacji klienckiej do aplikacji IoT Central platformy Azure](./tutorial-connect-device-nodejs.md).

Jeśli chcesz dowiedzieć się więcej o korzystaniu z IoT Central, sugerowane następne kroki to wypróbowanie przewodnika Szybki Start, rozpoczynając od [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md).
