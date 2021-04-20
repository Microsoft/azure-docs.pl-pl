---
title: Opracowywanie urządzeń dla Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie tworzenia urządzeń do łączenia się z aplikacją IoT Central aplikacji. Urządzenia używają telemetrii do wysyłania danych przesyłanych strumieniowo i właściwości do zgłaszania stanu urządzenia. Centrum IoT Central może ustawiać stan urządzenia przy użyciu właściwości zapisywalnych i wywołać polecenia na urządzeniu.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: ebd2759d4dfb8ee79130f9b4876eba8d45226d04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718795"
---
# <a name="iot-central-device-development-guide"></a>IoT Central tworzenia urządzeń

*Ten artykuł dotyczy deweloperów urządzeń.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest przeznaczony dla deweloperów urządzeń, którzy implementują kod do uruchamiania na urządzeniach, które łączą się z IoT Central.

Urządzenia współdziałają z aplikacją IoT Central przy użyciu następujących elementów pierwotnych:

- _Telemetria_ to dane wysyłane przez urządzenie do IoT Central. Na przykład strumień wartości temperatury z czujnika dołączania.
- _Właściwości_ to wartości stanu, które urządzenie zgłasza IoT Central. Na przykład bieżąca wersja oprogramowania układowego urządzenia. Możesz również mieć właściwości zapisywalne, które IoT Central aktualizować na urządzeniu, takie jak temperatura docelowa.
- _Polecenia_ są wywoływane z IoT Central, aby kontrolować zachowanie urządzenia. Na przykład aplikacja IoT Central może wywołać polecenie w celu ponownego uruchomienia urządzenia.

Konstruktor rozwiązań jest odpowiedzialny za konfigurowanie pulpitów nawigacyjnych i widoków w internetowym interfejsie użytkownika usługi IoT Central w celu wizualizacji telemetrii, zarządzania właściwościami i wywołań poleceń.

## <a name="types-of-device"></a>Typy urządzeń

W poniższych sekcjach opisano główne typy urządzeń, które można połączyć z IoT Central aplikacji:

### <a name="standalone-device"></a>Urządzenie autonomiczne

Urządzenie autonomiczne łączy się bezpośrednio z IoT Central. Urządzenie autonomiczne zazwyczaj wysyła dane telemetryczne z czujników dołączanych lub podłączonych do IoT Central aplikacji. Urządzenia autonomiczne mogą również zgłaszać wartości właściwości, odbierać wartości właściwości zapisywalnych i odpowiadać na polecenia.

### <a name="gateway-device"></a>Urządzenie bramy

Urządzenie bramy zarządza co najmniej jednym urządzeniem nadrzędnym, które łączą się z IoT Central aplikacji. Za pomocą IoT Central do konfigurowania relacji między urządzeniami nadrzędnymi i urządzeniem bramy. Aby dowiedzieć się więcej, zobacz Define a new IoT gateway device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia bramy [IoT w Azure IoT Central aplikacji).](./tutorial-define-gateway-device-type.md)

### <a name="edge-device"></a>Urządzenie brzegowe

Urządzenie brzegowe łączy się bezpośrednio z IoT Central, ale działa jako pośrednik dla innych urządzeń znanych jako _urządzenia liścia._ Urządzenie brzegowe zwykle znajduje się w pobliżu urządzeń liścia, dla których działa jako pośrednik. Scenariusze, które korzystają z urządzeń brzegowych, obejmują:

- Włącz urządzenia, które nie mogą łączyć się bezpośrednio z IoT Central nawiązać połączenia za pośrednictwem urządzenia brzegowego. Na przykład urządzenie liścia może używać połączenia Bluetooth do nawiązywania połączenia z urządzeniem brzegowym, które następnie łączy się przez Internet z IoT Central.
- Agregowanie danych telemetrycznych przed ich wysłaniem do IoT Central. Takie podejście może pomóc zmniejszyć koszty wysyłania danych do IoT Central.
- Kontroluj urządzenia liścia lokalnie, aby uniknąć opóźnień związanych z IoT Central przez Internet.

Urządzenie brzegowe może również wysyłać własne dane telemetryczne, zgłaszać swoje właściwości oraz reagować na zapisywalne aktualizacje właściwości i polecenia.

IoT Central tylko urządzenie brzegowe, a nie urządzenia liścia podłączone do urządzenia brzegowego.

Aby dowiedzieć się więcej, zobacz [Dodawanie Azure IoT Edge do aplikacji Azure IoT Central aplikacji.](./tutorial-add-edge-as-leaf-device.md)

## <a name="connect-a-device"></a>Łączenie urządzenia

Azure IoT Central używa usługi [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania całą rejestracją urządzeń i połączeniem.

Korzystanie z usługi DPS umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń na dużą skalę.
- Należy wygenerować poświadczenia urządzenia i skonfigurować urządzenia w trybie offline bez rejestrowania ich za pośrednictwem IoT Central użytkownika.
- Możesz użyć własnych identyfikatorów urządzeń do rejestrowania urządzeń w IoT Central. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami back-office.
- Pojedynczy, spójny sposób łączenia urządzeń z IoT Central.

Aby dowiedzieć się więcej, [zobacz Get connected to Azure IoT Central](./concepts-get-connected.md) and Best [practices (Uzyskiwanie](concepts-best-practices.md)połączenia z Azure IoT Central i Najlepsze rozwiązania).

### <a name="security"></a>Zabezpieczenia

Połączenie między urządzeniem a aplikacją IoT Central jest [](./concepts-get-connected.md#sas-group-enrollment) zabezpieczone przy użyciu sygnatur dostępu współdzielonych lub standardowych certyfikatów [X.509.](./concepts-get-connected.md#x509-group-enrollment)

### <a name="communication-protocols"></a>Protokoły komunikacyjne

Protokoły komunikacyjne, których urządzenie może używać do łączenia się z IoT Central m.in. MQTT, AMQP i HTTPS. Wewnętrznie IoT Central korzysta z centrum IoT, aby umożliwić łączność urządzenia. Aby uzyskać więcej informacji na temat protokołów komunikacyjnych, IoT Hub obsługuje łączność urządzeń, zobacz [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementowanie urządzenia

Szablon IoT Central urządzenia zawiera _model_ określający zachowania, które urządzenie tego typu powinno zaimplementować. Zachowania obejmują telemetrię, właściwości i polecenia.

> [!TIP]
> Możesz wyeksportować model z usługi IoT Central jako plik [JSON języka Digital Twins Definition Language (DTDL) w wersji 2.](https://github.com/Azure/opendigitaltwins-dtdl)

Każdy model ma unikatowy identyfikator modelu _bliźniaczej reprezentacji urządzenia_ (DTMI), taki jak `dtmi:com:example:Thermostat;1` . Gdy urządzenie łączy się z IoT Central, wysyła on kod DTMI implementego modelu. IoT Central następnie skojarzyć prawidłowy szablon urządzenia z urządzeniem.

[IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) definiuje zestaw konwencji, które urządzenie powinno stosować podczas implementowanie modelu DTDL.

Zestawy [SDK urządzeń Azure IoT](#languages-and-sdks) obejmują obsługę IoT Plug and Play wirtualnych.

### <a name="device-model"></a>Model urządzenia

Model urządzenia jest definiowany przy użyciu języka [DTDL.](https://github.com/Azure/opendigitaltwins-dtdl) Ten język umożliwia zdefiniowanie:

- Dane telemetryczne wysyłane przez urządzenie. Definicja zawiera nazwę i typ danych telemetrycznych. Na przykład urządzenie wysyła dane telemetryczne dotyczące temperatury jako podwójne.
- Właściwości, które urządzenie zgłasza do IoT Central. Definicja właściwości zawiera jej nazwę i typ danych. Na przykład urządzenie zgłasza stan as a boolean (wartość logiczna).
- Właściwości, które urządzenie może odbierać z IoT Central. Opcjonalnie można oznaczyć właściwość jako zapisywalny. Na przykład IoT Central docelową temperaturę jako dublet do urządzenia.
- Polecenia, na które reaguje urządzenie. Definicja zawiera nazwę polecenia oraz nazwy i typy danych dowolnych parametrów. Na przykład urządzenie reaguje na polecenie ponownego uruchomienia, które określa liczbę sekund oczekiwania przed ponownym uruchomieniem.

Model DTDL może być modelem _nieskładnikowym_ lub _wieloskładnikowym:_

- Model nieskładnikowy: Prosty model nie używa składników osadzonych ani kaskadowych. Wszystkie dane telemetryczne, właściwości i polecenia są definiowane jako pojedynczy _składnik domyślny_. Aby uzyskać przykład, zobacz [model termostatu.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)
- Model wieloskładnikowy. Bardziej złożony model, który zawiera co najmniej dwa składniki. Te składniki obejmują jeden składnik domyślny i co najmniej jeden dodatkowy zagnieżdżony składnik. Przykład można znaleźć w modelu [kontrolera](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) temperatury.

Aby dowiedzieć się więcej, [zobacz IoT Plug and Play modelowanie](../../iot-pnp/concepts-modeling-guide.md)

### <a name="conventions"></a>Konwencje

Urządzenie powinno być zgodne z IoT Plug and Play, gdy wymienia dane z IoT Central. Konwencje obejmują:

- Wyślij kod DTMI podczas połączenia z IoT Central.
- Wysyłaj poprawnie sformatowane ładunki i metadane JSON do IoT Central.
- Poprawnie odpowiadaj na zapisywalne właściwości i polecenia z IoT Central.
- Postępuj zgodnie z konwencjami nazewnictwa dla poleceń składników.

> [!NOTE]
> Obecnie IoT Central w pełni obsługuje tablicy **DTDL** i **typów danych geoprzestrzennych.**

Aby dowiedzieć się więcej o formacie komunikatów JSON, które urządzenie wymienia z IoT Central, zobacz Telemetria, właściwość i [ładunki poleceń](concepts-telemetry-properties-commands.md).

Aby dowiedzieć się więcej na temat IoT Plug and Play konwencji, zobacz [IoT Plug and Play konwencje.](../../iot-pnp/concepts-convention.md)

### <a name="device-sdks"></a>Zestawy SDK urządzeń

Użyj jednego z zestawów [SDK urządzeń Azure IoT,](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) aby zaimplementować zachowanie urządzenia. Kod powinien:

- Zarejestruj urządzenie w u usługi DPS i użyj informacji z usługi DPS, aby nawiązać połączenie z wewnętrznym centrum IoT w IoT Central aplikacji.
- Ogłaszaj kod DTMI modelu implementowany przez urządzenie.
- Wysyłaj dane telemetryczne w formacie, który określa model urządzenia. IoT Central używa modelu w szablonie urządzenia, aby określić, jak używać telemetrii do wizualizacji i analizy.
- Synchronizuj wartości właściwości między urządzeniem i IoT Central. Model określa nazwy właściwości i typy danych, aby IoT Central wyświetlić informacje.
- Zaim implementuj programy obsługi poleceń dla poleceń określonych w modelu. Model określa nazwy poleceń i parametry, których powinno używać urządzenie.

Aby uzyskać więcej informacji na temat roli szablonów urządzeń, zobacz [Co to są szablony urządzeń?](./concepts-device-templates.md).

Aby uzyskać przykładowy kod, [zobacz Create and connect a client application (Tworzenie i łączenie aplikacji klienckiej).](./tutorial-connect-device.md)

### <a name="languages-and-sdks"></a>Języki i zestawy SDK

Aby uzyskać więcej informacji na temat obsługiwanych języków i zestawów SDK, zobacz [Understand and use Azure IoT Hub device SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)(Opis i używanie Azure IoT Hub zestawów SDK urządzeń).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń i chcesz zagłębić się w kod, sugerowanym następnym krokiem jest utworzenie aplikacji klienckiej i połączenie jej z aplikacją Azure IoT Central [aplikacji.](./tutorial-connect-device.md)

Jeśli chcesz dowiedzieć się więcej na temat korzystania z IoT Central, sugerowane następne kroki to wypróbowanie przewodników Szybki start, począwszy od tematu Tworzenie Azure IoT Central [aplikacji.](./quick-deploy-iot-central.md)
