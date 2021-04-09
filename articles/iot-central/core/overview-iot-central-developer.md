---
title: Opracowywanie urządzeń na platformie Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie tworzenia urządzeń w celu nawiązania połączenia z aplikacją IoT Central. Urządzenia używają telemetrii do wysyłania danych i właściwości przesyłania strumieniowego w celu zgłoszenia stanu urządzenia. W usłudze IoT Central można ustawić stan urządzenia przy użyciu właściwości z możliwością zapisu i wywołań poleceń na urządzeniu.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 4423eea6c35ad44d55ad17f5703a95c6273422b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585863"
---
# <a name="iot-central-device-development-guide"></a>Przewodnik po opracowywaniu urządzenia IoT Central

*Ten artykuł ma zastosowanie do deweloperów urządzeń.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest przeznaczony dla deweloperów urządzeń, którzy implementują kod do uruchamiania na urządzeniach, które łączą się z IoT Central.

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

Aby dowiedzieć się więcej, zobacz temat [połączono z usługą Azure IoT Central](./concepts-get-connected.md) i [najlepszymi rozwiązaniami](concepts-best-practices.md).

### <a name="security"></a>Zabezpieczenia

Połączenie między urządzeniem a aplikacją IoT Central jest zabezpieczone przy użyciu [sygnatur dostępu współdzielonego](./concepts-get-connected.md#sas-group-enrollment) lub [certyfikatów X. 509](./concepts-get-connected.md#x509-group-enrollment)standardowych w branży.

### <a name="communication-protocols"></a>Protokoły komunikacyjne

Protokoły komunikacyjne używane przez urządzenie do nawiązywania połączeń IoT Central obejmują MQTT, AMQP i HTTPS. Wewnętrznie, IoT Central używa usługi IoT Hub, aby umożliwić łączność z urządzeniem. Aby uzyskać więcej informacji na temat protokołów komunikacyjnych obsługiwanych przez IoT Hub na potrzeby łączności urządzeń, zobacz [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementowanie urządzenia

Szablon urządzenia IoT Central obejmuje _model_ , który określa zachowania urządzenia tego typu. Zachowania obejmują dane telemetryczne, właściwości i polecenia.

> [!TIP]
> Model można wyeksportować z IoT Central jako plik JSON w [języku bliźniaczych reprezentacji Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) .

Każdy model ma unikatowy _Identyfikator modelu sznurka urządzenia_ (DTMI), taki jak `dtmi:com:example:Thermostat;1` . Gdy urządzenie łączy się z IoT Central, wysyła DTMI modelu, który implementuje. IoT Central następnie może skojarzyć właściwy szablon urządzenia z urządzeniem.

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) definiuje zestaw Konwencji, które powinny być stosowane przez urządzenie, gdy IMPLEMENTUJE model DTDL.

[Zestawy SDK urządzeń Azure IoT](#languages-and-sdks) obejmują obsługę Konwencji Plug and Play IoT.

### <a name="device-model"></a>Model urządzenia

Model urządzenia jest definiowany przy użyciu [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Ten język pozwala definiować:

- Dane telemetryczne wysyłane przez urządzenie. Definicja zawiera nazwę i typ danych telemetrii. Na przykład urządzenie wysyła dane telemetryczne temperatury jako podwójną.
- Właściwości, które urządzenie ma IoT Central. Definicja właściwości zawiera jej nazwę i typ danych. Na przykład urządzenie raportuje stan zaworu jako wartość logiczną.
- Właściwości, z których urządzenie może odbierać IoT Central. Opcjonalnie można oznaczyć właściwość jako zapisywalną. Na przykład IoT Central wysyła do urządzenia docelową temperaturę jako podwójną.
- Polecenia, na które urządzenie reaguje. Definicja zawiera nazwę polecenia oraz nazwy i typy danych parametrów. Na przykład urządzenie odpowiada na polecenie ponownego uruchomienia, które określa czas oczekiwania (w sekundach) przed ponownym uruchomieniem.

Model DTDL może być modelem _no-Component_ lub _wieloskładnikowym_ :

- Model bez składników: prosty model nie używa składników osadzonych ani kaskadowych. Wszystkie dane telemetryczne, właściwości i polecenia są definiowane jako jeden _składnik domyślny_. Aby zapoznać się z przykładem, zobacz model [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .
- Model wieloskładnikowy. Bardziej skomplikowany model, który zawiera co najmniej dwa składniki. Te składniki obejmują jeden składnik domyślny i jeden lub więcej dodatkowych składników zagnieżdżonych. Aby zapoznać się z przykładem, zobacz model [kontrolera temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) .

Aby dowiedzieć się więcej, zobacz [Przewodnik dotyczący modelowania Plug and Play IoT](../../iot-pnp/concepts-modeling-guide.md)

### <a name="conventions"></a>Konwencje

Urządzenie powinno przestrzegać Konwencji Plug and Play IoT, gdy wymienia dane z IoT Central. Konwencje obejmują:

- Wyślij DTMI, gdy nawiąże połączenie z IoT Central.
- Wysyłaj poprawnie sformatowane ładunki JSON i metadane do IoT Central.
- Prawidłowo odpowiadaj na zapisywalne właściwości i polecenia z IoT Central.
- Postępuj zgodnie z konwencjami nazewnictwa dla poleceń składnika.

> [!NOTE]
> Obecnie IoT Central nie obsługuje w pełni **macierzy** DTDL i typów danych **geoprzestrzennych** .

Aby dowiedzieć się więcej o formacie komunikatów JSON wymienianych przez urządzenie z IoT Central, zobacz dane [telemetryczne, właściwości i polecenia](concepts-telemetry-properties-commands.md).

Aby dowiedzieć się więcej na temat Konwencji Plug and Play IoT, zobacz [konwencje Plug and Play IoT](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>Zestawy SDK urządzeń

Aby zaimplementować zachowanie urządzenia, użyj jednego z [zestawów SDK urządzeń usługi Azure IoT](#languages-and-sdks) . Kod powinien:

- Zarejestruj urządzenie w usłudze DPS i Skorzystaj z informacji z usługi DPS, aby nawiązać połączenie z wewnętrznym centrum IoT Hub w aplikacji IoT Central.
- Ogłoś DTMI modelu, który implementuje urządzenie.
- Wysyłaj dane telemetryczne w formacie określanym przez model urządzenia. IoT Central korzysta z modelu w szablonie urządzenia, aby określić, jak używać telemetrii do wizualizacji i analizy.
- Zsynchronizuj wartości właściwości między urządzeniem a IoT Central. Model określa nazwy właściwości i typy danych, dzięki czemu IoT Central mogą wyświetlać informacje.
- Implementowanie obsługi poleceń dla poleceń określonych w modelu. Model określa nazwy poleceń i parametry, które powinny być używane przez urządzenie.

Aby uzyskać więcej informacji na temat roli szablonów urządzeń, zobacz [co to są szablony urządzeń?](./concepts-device-templates.md)

Aby zapoznać się z przykładowym kodem, zobacz [Tworzenie i łączenie aplikacji klienckiej](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Języki i zestawy SDK

Aby uzyskać więcej informacji na temat obsługiwanych języków i zestawów SDK, zobacz [Omówienie i używanie zestawów SDK urządzeń IoT Hub platformy Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia i chcesz szczegółowe do pewnego kodu, sugerowanym następnym krokiem jest [utworzenie i podłączenie aplikacji klienckiej do aplikacji IoT Central platformy Azure](./tutorial-connect-device.md).

Jeśli chcesz dowiedzieć się więcej o korzystaniu z IoT Central, sugerowane następne kroki to wypróbowanie przewodnika Szybki Start, rozpoczynając od [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md).
