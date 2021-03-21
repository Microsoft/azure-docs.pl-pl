---
title: Omówienie symulacji urządzenia — Azure | Microsoft Docs
description: Opis akceleratora rozwiązania dla symulacji urządzenia i jego możliwości.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 7be0278fd8870395b8495168eb9723780eba4b26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852374"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązań do symulacji urządzeń

W przypadku rozwiązania IoT opartego na chmurze urządzenia nawiązują połączenie z punktem końcowym w chmurze w celu wysyłania danych telemetrycznych, takich jak temperatura, lokalizacja i stan. Twoje rozwiązanie zużywa te dane telemetryczne, umożliwiając podejmowanie akcji lub uzyskiwanie szczegółowych informacji z nich.

Podczas opracowywania rozwiązania IoT, eksperymentowanie i testowanie są zasadniczymi częściami tego procesu. Symulacja jest ważnym narzędziem w ramach tego procesu. Symulacja urządzenia pozwala:

* Szybko Rozpocznij prototyp, a następnie wykonaj iterację, dostosowując symulowane zachowanie urządzenia na bieżąco. Ten proces pozwala udowodnić pomysł przed inwestycją w koszty sprzętowe. Można utworzyć niestandardowe urządzenia za pomocą interfejsu użytkownika sieci Web w celu wygenerowania prototypowego urządzenia w kilka sekund.
* Sprawdź, czy rozwiązanie działa zgodnie z oczekiwaniami z urządzenia do rozwiązania, symulując rzeczywiste zachowania urządzeń. Można tworzyć skrypty złożonych zachowań urządzenia przy użyciu języka JavaScript, aby generować realistyczne symulowane dane telemetryczne.
* Skalowanie Przetestuj swoje rozwiązanie, symulując normalne, szczytowe i wykraczające poza szczytowe warunki ładowania. Testy skalowania pomagają również w prawidłowym rozmiarze zasobów platformy Azure potrzebnych do uruchomienia rozwiązania.

![Przykładowa symulacja drona](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Symulacja urządzenia umożliwia definiowanie modeli urządzeń w celu symulowania rzeczywistych urządzeń. Ten model zawiera formaty wiadomości, właściwości sznurka i metody. Możesz również symulować złożone zachowania urządzeń za pomocą języka JavaScript.

Można uruchamiać symulacje dla jednej do tysięcy urządzeń łączących się z dowolnym Centrum IoT Hub. Aby pomóc w testowaniu, można opcjonalnie wdrożyć Centrum IoT Hub wraz z symulacją urządzenia dla środowiska autonomicznego.

Symulacja urządzenia jest bezpłatna. Jednak symulacja urządzenia jest wdrażana w ramach subskrypcji platformy Azure w chmurze i zużywa zasoby platformy Azure. Jeśli symulacja urządzenia nie spełnia wymagań użytkownika, [kod źródłowy jest również dostępny w witrynie GitHub](https://github.com/Azure/device-simulation-dotnet) , aby można było go kopiować i modyfikować.

## <a name="sample-simulations"></a>Przykładowe symulacje

Po wdrożeniu symulacji urządzenia uzyskasz kilka przykładowych symulacji i przykładowych urządzeń. Możesz użyć tych przykładów, aby dowiedzieć się, jak korzystać z symulacji urządzenia. Aby rozpocząć, uruchom [przykładową symulację](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md). Możesz również [utworzyć własną symulację przy użyciu jednego z wielu dostępnych przykładowych urządzeń](iot-accelerators-device-simulation-create-simulation.md).

![Konfiguracja symulacji](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Symulowane urządzenia niestandardowe

Możesz użyć symulacji urządzenia, aby [utworzyć niestandardowe modele urządzeń](iot-accelerators-device-simulation-create-custom-device.md) do użycia w symulacjach. Na przykład można zdefiniować nowy model urządzenia chłodziarki, który wysyła dane telemetryczne temperatury i wilgotności. Niestandardowo symulowane urządzenia są idealnym rozwiązaniem dla prostych zachowań urządzeń z losowymi, zwiększaniem lub zmniejszaniem wartości telemetrii.

![Tworzenie modelu urządzenia](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Zaawansowane symulowane urządzenia

Gdy potrzebna jest większa kontrola nad wartościami telemetrii wysyłanymi przez urządzenie, można użyć zaawansowanego modelu urządzenia. Zaawansowane modele urządzeń umożliwiają obsługę języka JavaScript w celu manipulowania wysłanych wartości telemetrycznych. Na przykład można zasymulować wewnętrznej temperatury zaparkowanego samochodu na gorącą Sunny dzień — jako zewnętrzny wzrost temperatury, wewnętrzna temperatura wzrasta wykładniczo.

Zaawansowane modele urządzeń umożliwiają [Tworzenie i przekazywanie własnych modeli urządzeń](iot-accelerators-device-simulation-advanced-device.md) , które składają się z pliku definicji urządzenia JSON i odpowiednich plików JavaScript.

Zaawansowane modele urządzeń pozwalają:

* Określ format wiadomości wysyłanej z urządzenia wraz z typami telemetrii.
* Za pomocą niestandardowych skryptów można generować wartości telemetryczne, które utrzymują stan urządzenia w czasie.
* Użyj niestandardowych skryptów, aby określić, jak symulowane urządzenie odpowiada na metody.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje na temat akceleratora rozwiązania dla symulacji urządzenia i jego możliwości. Aby wdrożyć Akcelerator rozwiązania, odwiedź repozytorium GitHub:

> [!div class="nextstepaction"]
> [wdrażanie i uruchamianie symulacji urządzenia IoT na platformie Azure](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)
