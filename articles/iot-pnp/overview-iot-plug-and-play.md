---
title: Wprowadzenie do wersji zapoznawczej i odtwórczych IoT | Dokumenty firmy Microsoft
description: Dowiedz się więcej o podglądzie i odtworzenia IoT. Funkcja IoT Plug and Play jest oparta na otwartym języku modelowania, który umożliwia urządzeniom IoT deklarowanie ich możliwości. Urządzenia IoT prezentują tę deklarację, nazywaną modelem możliwości urządzenia, gdy łączą się z rozwiązaniami w chmurze, takimi jak Azure IoT Central lub aplikacje partnerskie. Rozwiązanie w chmurze może następnie automatycznie zrozumieć urządzenie i rozpocząć interakcję z nim — wszystko to bez pisania kodu.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770434"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Co to jest podgląd ioT Plug and Play?

IoT Plug and Play Preview umożliwia deweloperom rozwiązań integrowanie urządzeń z ich rozwiązaniami bez pisania kodu osadzonego. Sednem IoT Plug and Play jest schemat _modelu możliwości urządzenia,_ który opisuje możliwości urządzenia. Ten schemat jest dokument JSON, który jest skonstruowany jako zestaw interfejsów, które zawierają definicje:

- _Właściwości reprezentujące_ stan tylko do odczytu i odczytu/zapisu urządzenia lub innej jednostki. Na przykład numer seryjny urządzenia może być właściwością tylko do odczytu, a temperatura docelowa na termostatze może być właściwością odczytu/zapisu.
- _Dane telemetryczne,_ które są danymi emitowanymi przez urządzenie, niezależnie od tego, czy dane są zwykłym strumieniem odczytów czujników, sporadycznym błędem lub komunikatem informacyjnym.
- _Polecenia opisujące_ funkcję lub operację, które można wykonać na urządzeniu. Na przykład polecenie może ponownie uruchomić bramę lub zrobić zdjęcie za pomocą zdalnej kamery.

Interfejsy można ponownie używać w różnych modelach możliwości urządzeń, aby ułatwić współpracę i przyspieszyć rozwój.

Aby usługa IoT Plug and Play bezproblemowo działała z [usługą Azure Digital Twins,](../digital-twins/about-digital-twins.md)schemat IoT Plug and Play jest definiowany przy użyciu [języka Cyfrowej Bliźniaczej definicji (DTDL).](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) IoT Plug and Play i DTDL są otwarte dla społeczności, a firma Microsoft z zadowoleniem przyjmuje współpracę z klientami, partnerami i branżą. Oba są oparte na otwartych standardach W3C, takich jak JSON-LD i RDF, które umożliwiają łatwiejsze wdrażanie usług i narzędzi. Ponadto korzystanie z funkcji IoT Plug and Play i DTDL nie ma żadnych dodatkowych kosztów. Stawki standardowe dla [usługi Azure IoT Hub,](../iot-hub/about-iot-hub.md) [Usługi Azure IoT Central](../iot-central/core/overview-iot-central.md)i innych usług platformy Azure pozostają takie same.

Rozwiązania oparte na Uiście IoT Hub lub IoT Central mogą korzystać z technologii IoT Plug and Play.

W tym artykule opisano:

- Typowe role skojarzone z projektem, który używa IoT Plug and Play.
- Jak korzystać z urządzeń IoT Plug and Play w aplikacji.
- Jak opracować aplikację urządzenia IoT, która obsługuje IoT Plug and Play.
- Jak certyfikować urządzenie IoT Plug and Play i publikować w [katalogu urządzeń Certified for IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Role użytkownika

IoT Plug and Play jest przydatne dla dwóch typów programistów:

- _Deweloper rozwiązania_ jest odpowiedzialny za tworzenie rozwiązania IoT przy użyciu usługi Azure IoT i innych zasobów platformy Azure oraz za identyfikowanie urządzeń IoT w celu integracji.
- _Deweloper urządzenia_ tworzy kod, który działa na urządzeniu podłączonym do rozwiązania.

## <a name="use-iot-plug-and-play-devices"></a>Korzystanie z urządzeń Typu Plug and Play ioT

Jako deweloper rozwiązania możesz opracować rozwiązanie IoT hostowane w chmurze, które używa urządzeń IoT Plug and Play. Można użyć jednej z następujących usług platformy Azure:

- [IoT Central](../iot-central/core/overview-iot-central.md) - w pełni zarządzane rozwiązanie do obsługi oprogramowania IoT jako usługi, które ułatwia tworzenie produktów łączących światy fizyczne i cyfrowe.
- [Usługa IoT Hub](../iot-hub/about-iot-hub.md) — zarządzana usługa w chmurze, która działa jako centrum komunikatów dla bezpiecznej, dwukierunkowej komunikacji między aplikacją IoT a urządzeniami.

Urządzenia IoT Plug and Play można znaleźć za pośrednictwem katalogu urządzeń z certyfikatem platformy Azure dla IoT. Każde urządzenie Typu Plug and Play ioT w katalogu zostało zweryfikowane i ma model możliwości urządzenia. Wyświetl model możliwości urządzenia, aby zrozumieć funkcjonalność urządzenia lub użyć go do symulacji urządzenia w usłudze Azure IoT Central.

Po podłączeniu urządzenia Typu Plug and Play usługi IoT można wyświetlić jego model możliwości urządzenia, interfejsy zawarte w modelu oraz dane telemetryczne, właściwości i polecenia zdefiniowane w tych interfejsach.

## <a name="develop-an-iot-device-application"></a>Tworzenie aplikacji na urządzenie IoT

Jako deweloper urządzeń możesz opracować produkt sprzętowy IoT, który obsługuje funkcję IoT Plug and Play. Proces ten obejmuje dwa kluczowe etapy:

1. Zdefiniuj model możliwości urządzenia i interfejsy. Autorem zestawu plików JSON, które deklarują możliwości urządzenia za pomocą [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Model możliwości urządzenia opisuje pełną jednostkę, taką jak produkt fizyczny, i definiuje zestaw interfejsów zaimplementowanych przez tę jednostkę. Interfejsy są umów udostępnionych, które jednoznacznie identyfikują dane telemetryczne, właściwości i polecenia obsługiwane przez urządzenie. Interfejsy mogą być ponownie stosowane w różnych modelach możliwości urządzenia.

1. Utwórz oprogramowanie urządzenia lub oprogramowanie układowe, które implementuje funkcje zadeklarowane w modelu możliwości urządzenia i interfejsach. Zestaw SDK usługi Azure IoT zawiera interfejsy API do implementowania modeli możliwości urządzenia.

Pakiet rozszerzeń [Narzędzia IoT platformy Azure dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) zawiera wiele funkcji ułatwiających. Na przykład jako deweloper urządzeń można użyć rozszerzenia do generowania szkieletu projektu C z modelu możliwości. Jednak można użyć dowolnego IDE do tworzenia i implementowania modeli możliwości urządzenia.

## <a name="certify-an-iot-plug-and-play-device"></a>Certyfikuj urządzenie Typu Plug and Play IoT

Jako deweloper urządzeń możesz przesłać produkty sprzętowe IoT do certyfikacji. Certyfikowane urządzenie można opublikować w katalogu urządzeń certyfikowanych dla IoT. Etapy procesu certyfikacji obejmują:

- Dołącz do [sieci partnerów firmy Microsoft](https://partner.microsoft.com).
- Dołączanie do portalu Certified for Azure IoT.
- Prześlij model możliwości urządzenia IoT Plug and Play i informacje marketingowe, aby utworzyć nowy rekord urządzenia.
- Zdaj automatyczne testy sprawdzania poprawności urządzenia.
- Publikowanie w katalogu urządzeń Certified for IoT.

## <a name="regional-availability"></a>Dostępność regionalna

Podczas publicznej wersji zapoznawczej ioT Plug and Play jest dostępny we wszystkich regionach.

## <a name="message-quotas-in-iot-hub"></a>Przydziały wiadomości w Centrum IoT
Podczas publicznej wersji zapoznawczej urządzenia IoT Plug and Play wysyłają oddzielne wiadomości na interfejs, co może zwiększyć liczbę wiadomości zaliczonych do [przydziału wiadomości.](../iot-hub/iot-hub-devguide-quotas-throttling.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd IoT Plug and Play, sugerowanym następnym krokiem jest wypróbowanie jednego z szybkich startów:

- [Tworzenie urządzenia Typu Plug and Play za pomocą modelu funkcji urządzenia](./quickstart-create-pnp-device-windows.md)
- [Łączenie urządzenia z usługą IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Nawiązywanie połączenia z urządzeniem w rozwiązaniu](./quickstart-connect-pnp-device-solution-node.md)
