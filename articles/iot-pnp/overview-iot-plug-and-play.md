---
title: Wprowadzenie do wersji zapoznawczej IoT Plug and Play | Microsoft Docs
description: Dowiedz się więcej o usłudze IoT Plug and Play Preview. Plug and Play IoT jest oparta na otwartym języku modelowania, który umożliwia urządzeniom IoT zadeklarować swoje możliwości. Urządzenia IoT stanowią tę deklarację nazywaną modelem możliwości urządzenia, gdy łączą się z rozwiązaniami w chmurze, takimi jak Azure IoT Central lub Aplikacje partnerskie. Rozwiązanie w chmurze może następnie automatycznie zrozumieć urządzenie i zacząć z niego korzystać — wszystko to bez konieczności pisania kodu.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770434"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Co to jest IoT Plug and Play w wersji zapoznawczej?

Wersja zapoznawcza Plug and Play IoT umożliwia deweloperom rozwiązań integrację urządzeń z ich rozwiązaniami bez konieczności pisania kodu osadzonego. Podstawą Plug and Play IoT jest schemat _modelu możliwości urządzenia_ , który opisuje możliwości urządzeń. Ten schemat jest dokumentem JSON, który jest strukturalny jako zestaw interfejsów, które obejmują definicje:

- _Właściwości_ reprezentujące stan tylko do odczytu i odczytu/zapisu urządzenia lub innej jednostki. Na przykład numer seryjny urządzenia może być właściwością tylko do odczytu, a docelowa temperatura w termostatie może być właściwością odczytu/zapisu.
- Dane _telemetryczne_ , które są emitowane przez urządzenie, niezależnie od tego, czy dane są regularnym strumieniem odczytów czujnika, okazjonalnym błędem czy komunikatem informacyjnym.
- _Polecenia_ opisujące funkcję lub operację, którą można wykonać na urządzeniu. Na przykład polecenie może ponownie uruchomić bramę lub zrobić zdjęcie przy użyciu zdalnej kamery.

Można ponownie użyć interfejsów między modelami możliwości urządzeń, aby ułatwić współpracę i przyspieszyć programowanie.

Aby usługa IoT Plug and Play bezproblemowo korzystać z [usługi Azure Digital bliźniaczych reprezentacji](../digital-twins/about-digital-twins.md), schemat Plug and Play IoT zostanie zdefiniowany przy użyciu [języka Digital sznur Definition Language (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Plug and Play IoT i DTDL są otwarte dla społeczności, a firma Microsoft zachęca do współpracy z klientami, partnerami i branżą. Oba są oparte na otwartych standardach W3C, takich jak JSON-LD i RDF, które umożliwiają łatwiejsze wdrażanie w ramach usług i narzędzi. Ponadto nie ma dodatkowych kosztów związanych z korzystaniem z usługi IoT Plug and Play i DTDL. Standardowe stawki za [usługę azure IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/core/overview-iot-central.md)i inne usługi platformy Azure pozostają takie same.

Rozwiązania oparte na IoT Hub lub IoT Central mogą korzystać z usługi IoT Plug and Play.

Ten artykuł zawiera opis:

- Typowe role skojarzone z projektem, który używa Plug and Play IoT.
- Jak korzystać z urządzeń Plug and Play IoT w aplikacji.
- Jak opracowywać aplikację urządzenia IoT, która obsługuje Plug and Play IoT.
- Jak zatwierdzić urządzenie Plug and Play IoT i opublikować je w [wykazie urządzeń z certyfikatem usługi IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Role użytkownika

Plug and Play IoT jest przydatne dla dwóch typów deweloperów:

- _Deweloper rozwiązań_ jest odpowiedzialny za opracowywanie rozwiązania IoT przy użyciu usługi Azure IoT i innych zasobów platformy Azure oraz do identyfikowania urządzeń IoT do integracji.
- _Deweloper urządzenia_ tworzy kod, który jest uruchamiany na urządzeniu podłączonym do rozwiązania.

## <a name="use-iot-plug-and-play-devices"></a>Korzystanie z urządzeń Plug and Play IoT

Jako programista rozwiązań możesz opracowywać rozwiązanie IoT hostowane w chmurze, które korzysta z urządzeń Plug and Play IoT. Możesz użyć jednej z następujących usług platformy Azure:

- [IoT Central](../iot-central/core/overview-iot-central.md) — w pełni zarządzane rozwiązanie typu "oprogramowanie jako usługa IoT", które ułatwia tworzenie produktów łączących fizyczne i cyfrowe środowiska światowe.
- [IoT Hub](../iot-hub/about-iot-hub.md) — zarządzana usługa w chmurze, która działa jako centrum komunikatów w celu bezpiecznej, dwukierunkowej komunikacji między aplikacją IoT i urządzeniami.

Urządzenia IoT Plug and Play można znaleźć za pomocą wykazu urządzeń z certyfikatem platformy Azure dla IoT. Każde urządzenie Plug and Play IoT w wykazie zostało zweryfikowane i ma model możliwości urządzenia. Wyświetl model możliwości urządzenia, aby zrozumieć funkcjonalność urządzenia lub użyć go do symulowania urządzenia w usłudze Azure IoT Central.

Po nawiązaniu połączenia z urządzeniem IoT Plug and Play można wyświetlić jego model możliwości urządzenia, interfejsy zawarte w modelu oraz dane telemetryczne, właściwości i polecenia zdefiniowane w tych interfejsach.

## <a name="develop-an-iot-device-application"></a>Opracowywanie aplikacji urządzenia IoT

Jako deweloper urządzenia możesz opracować produkt sprzętu IoT, który obsługuje Plug and Play IoT. Proces obejmuje dwa kluczowe kroki:

1. Zdefiniuj model możliwości urządzenia i interfejsy. Tworzysz zestaw plików JSON, które deklarują możliwości urządzenia przy użyciu [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Model możliwości urządzenia opisuje kompletną jednostkę, taką jak produkt fizyczny, i definiuje zestaw interfejsów implementowanych przez tę jednostkę. Interfejsy są wspólnymi kontraktami, które jednoznacznie identyfikują dane telemetryczne, właściwości i polecenia obsługiwane przez urządzenie. Interfejsy mogą być ponownie używane w różnych modelach możliwości urządzeń.

1. Utwórz oprogramowanie urządzenia lub oprogramowanie układowe, które implementuje możliwości zadeklarowane w modelu możliwości urządzenia i interfejsy. Zestaw SDK usługi Azure IoT zawiera interfejsy API służące do implementowania modeli możliwości urządzeń.

Pakiet Microsoft [Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack udostępnia wiele funkcji, które ułatwią Ci pomoc. Na przykład jako deweloper urządzenia można użyć rozszerzenia do wygenerowania szkieletowego projektu C z modelu możliwości. Można jednak użyć dowolnego środowiska IDE do tworzenia i implementowania modeli możliwości urządzeń.

## <a name="certify-an-iot-plug-and-play-device"></a>Certyfikowanie urządzenia Plug and Play IoT

Jako deweloper urządzenia możesz przesłać produkty sprzętu IoT do certyfikacji. Certyfikowane urządzenie można opublikować w wykazie certyfikowane urządzenia IoT. Kroki procesu certyfikacji obejmują:

- Dołącz do [Microsoft Partner Network](https://partner.microsoft.com).
- Dołączanie do portalu usługi Azure IoT z certyfikatem.
- Prześlij model możliwości urządzenia Plug and Play IoT i informacje marketingowe, aby utworzyć nowy rekord urządzenia.
- Przekaż zautomatyzowany zestaw testów weryfikacyjnych dla urządzenia.
- Publikowanie w katalogu certyfikowanych urządzeń IoT.

## <a name="regional-availability"></a>Dostępność regionalna

W publicznej wersji zapoznawczej Plug and Play IoT jest dostępny we wszystkich regionach.

## <a name="message-quotas-in-iot-hub"></a>Limity przydziału komunikatów w IoT Hub
W publicznej wersji zapoznawczej urządzenia IoT Plug and Play wysyłają osobne komunikaty na interfejs, co może zwiększyć liczbę komunikatów zliczanych w ramach [limitu przydziału komunikatów](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd Plug and Play IoT, sugerowanym następnym krokiem jest wypróbowanie jednego z przewodników szybki start:

- [Tworzenie urządzenia Plug and Play IoT przy użyciu modelu możliwości urządzenia](./quickstart-create-pnp-device-windows.md)
- [Łączenie urządzenia z usługą IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Nawiązywanie połączenia z urządzeniem w rozwiązaniu](./quickstart-connect-pnp-device-solution-node.md)
