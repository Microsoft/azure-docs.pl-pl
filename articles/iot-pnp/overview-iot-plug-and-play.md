---
title: Wprowadzenie do IoT Plug and Play | Microsoft Docs
description: Dowiedz się więcej IoT Plug and Play. IoT Plug and Play opiera się na otwartym języku modelowania, który umożliwia inteligentnym urządzeniem IoT deklarowanie swoich możliwości. Urządzenia IoT prezentują deklarację , nazywaną modelem urządzenia, podczas łączenia się z rozwiązaniami w chmurze. Rozwiązanie w chmurze może następnie automatycznie zrozumieć urządzenie i rozpocząć z nim interakcję, a wszystko to bez pisania kodu.
author: rido-min
ms.author: rmpablos
ms.date: 03/21/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 91a09db16524ebc7e4c04069b69b1c42c67538c6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739714"
---
# <a name="what-is-iot-plug-and-play"></a>Co to jest usługa IoT Plug and Play?

Usługa IoT Plug and Play umożliwia konstruktorom rozwiązań integrację urządzeń inteligentnych z ich rozwiązaniami bez konieczności ręcznej konfiguracji. Podstawą usługi IoT Plug and Play jest _model_ urządzenia używany przez urządzenie do anonsowania swoich możliwości do IoT Plug and Play z obsługą aplikacji. Ten model ma strukturę zestawu elementów, które definiują:

- _Właściwości,_ które reprezentują stan tylko do odczytu lub zapisywalny urządzenia lub innej jednostki. Na przykład numer seryjny urządzenia może być właściwością tylko do odczytu, a temperatura docelowa termostatu może być właściwością zapisywalna.
- _Dane_ telemetryczne, które są danymi emitowanych przez urządzenie, niezależnie od tego, czy są zwykłym strumieniem odczytów z czujników, okazjonalnych błędów czy komunikatami informacyjnymi.
- _Polecenia_ opisujące funkcję lub operację, które można wykonać na urządzeniu. Na przykład polecenie może ponownie uruchomić bramę lub zrobić zdjęcie za pomocą aparatu zdalnego.

Te elementy można grupować w interfejsach, aby ponownie używać modeli, aby ułatwić współpracę i przyspieszyć opracowywanie.

Aby IoT Plug and Play z usługą [Azure Digital Twins,](../digital-twins/overview.md)należy zdefiniować modele i interfejsy przy użyciu [języka Digital Twins Definition Language (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) IoT Plug and Play i DTDL są otwarte dla społeczności, a firma Microsoft zaprasza do współpracy z klientami, partnerami i branżą. Oba są oparte na otwartych standardach W3C, takich jak JSON-LD i RDF, co umożliwia łatwiejsze wdrożenie w usługach i narzędziach.

Korzystanie z języka DTDL i języka IoT Plug and Play nie ma żadnych dodatkowych kosztów. Standardowe stawki dla [Azure IoT Hub](../iot-hub/about-iot-hub.md) i innych usług platformy Azure pozostają takie same.

Ten artykuł zawiera opis:

- Typowe role skojarzone z projektem, który używa IoT Plug and Play.
- Jak używać IoT Plug and Play w aplikacji.
- Jak opracować aplikację urządzenia IoT, która obsługuje IoT Plug and Play.

## <a name="user-roles"></a>Role użytkownika

IoT Plug and Play jest przydatna dla dwóch typów deweloperów:

- Konstruktor _rozwiązań jest_ odpowiedzialny za opracowywanie rozwiązania IoT przy użyciu usług Azure IoT Hub i innych zasobów platformy Azure oraz identyfikowanie urządzeń IoT do integracji.
- Konstruktor _urządzenia tworzy_ kod uruchamiany na urządzeniu połączonym z rozwiązaniem.

## <a name="use-iot-plug-and-play-devices"></a>Korzystanie z IoT Plug and Play urządzeń

Konstruktor rozwiązań może używać [](../iot-central/core/overview-iot-central.md) usługi IoT Central lub [IoT Hub](../iot-hub/about-iot-hub.md) do tworzenia hostowanych w chmurze rozwiązań IoT, które korzysta IoT Plug and Play urządzeń.

Internetowy interfejs użytkownika w IoT Central umożliwia monitorowanie warunków urządzenia, tworzenie reguł oraz zarządzanie milionami urządzeń i ich danych w całym cyklu życia. IoT Plug and Play łączą się bezpośrednio z aplikacją IoT Central, w której można monitorować i kontrolować urządzenia za pomocą dostosowywalnych pulpitów nawigacyjnych. Do tworzenia i edytowania modeli DTDL można IoT Central szablonów urządzeń w internetowym interfejsie użytkownika.

IoT Hub — zarządzana usługa w chmurze — działa jako centrum komunikatów do bezpiecznej, dwukierunkowej komunikacji między aplikacją IoT i urządzeniami. Po połączeniu urządzenia IoT Plug and Play z centrum IoT hub możesz użyć narzędzia eksploratora usługi [Azure IoT,](./howto-use-iot-explorer.md) aby wyświetlić dane telemetryczne, właściwości i polecenia zdefiniowane w modelu DTDL.

Jeśli masz istniejące czujniki dołączone do bramy systemu Windows lub Linux, możesz użyć mostka [IoT Plug and Play](./concepts-iot-pnp-bridge.md), aby podłączyć te czujniki i utworzyć urządzenia IoT Plug and Play bez konieczności pisania oprogramowania/oprogramowania układowego urządzenia (dla obsługiwanych protokołów). [](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)

## <a name="develop-an-iot-device-application"></a>Tworzenie aplikacji urządzenia IoT

Jako konstruktor urządzenia możesz opracować produkt sprzętowy IoT, który obsługuje IoT Plug and Play. Proces składa się z trzech kluczowych kroków:

1. Zdefiniuj model urządzenia. Tworzenie zestawu plików JSON definiującego możliwości urządzenia przy użyciu języka [DTDL.](https://github.com/Azure/opendigitaltwins-dtdl) Model opisuje kompletną jednostkę, taką jak produkt fizyczny, i definiuje zestaw interfejsów implementowany przez jednostkę. Interfejsy to udostępnione kontrakty, które jednoznacznie identyfikują dane telemetryczne, właściwości i polecenia obsługiwane przez urządzenie. Interfejsy mogą być ponownie używane w różnych modelach.

1. Tworzenie oprogramowania lub oprogramowania układowego urządzenia w taki sposób, aby jego dane telemetryczne, właściwości i polecenia IoT Plug and Play konwencje. Jeśli łączysz istniejące czujniki dołączone do bramy systemu Windows lub Linux, [mostek IoT Plug and Play](./concepts-iot-pnp-bridge.md) ten krok może uprościć.

1. Urządzenie ogłasza identyfikator modelu w ramach połączenia MQTT. Zestaw SDK usługi Azure IoT zawiera nowe konstrukcje, które zapewniają identyfikator modelu w czasie połączenia.

> [!Important]
> IoT Plug and Play muszą używać MQTT lub MQTT za pośrednictwem sieci WebSocket. Inne protokoły, takie jak AMQP lub HTTP, nie są prawidłowe do implementowania IoT Plug and Play urządzeń.

## <a name="device-certification"></a>Certyfikacja urządzenia

Program [IoT Plug and Play certyfikacji urządzeń weryfikuje,](../certification/program-requirements-pnp.md) czy urządzenie spełnia IoT Plug and Play certyfikacji. Certyfikowane urządzenie można dodać do publicznego katalogu urządzeń [certyfikowanych dla usługi Azure IoT.](https://aka.ms/devicecatalog)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już omówienie IoT Plug and Play, sugerowanym następnym krokiem jest wypróbowanie jednego z przewodników Szybki start:

- [Łączenie urządzenia z usługą IoT Hub](./quickstart-connect-device.md)
- [Interakcja z urządzeniem z rozwiązania](./quickstart-service.md)
