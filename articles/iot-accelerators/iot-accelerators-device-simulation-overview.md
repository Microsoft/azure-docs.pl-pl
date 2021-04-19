---
title: Omówienie symulacji urządzeń — Azure | Microsoft Docs
description: Opis akceleratora rozwiązania do symulacji urządzeń i jego możliwości.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 27a23ff924c2fa9e9e35fec010ca2a177868eacc
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713916"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązań do symulacji urządzeń

W opartym na chmurze rozwiązaniu IoT urządzenia łączą się z punktem końcowym w chmurze w celu wysyłania danych telemetrycznych, takich jak temperatura, lokalizacja i stan. Twoje rozwiązanie zużywa tę telemetrię, umożliwiając akcje lub pobierać z niego szczegółowe informacje.

Podczas opracowywania rozwiązania IoT eksperymentowanie i testowanie są istotnymi elementami tego procesu. Symulacja jest ważnym narzędziem w całym tym procesie. Symulacja urządzenia umożliwia:

* Szybko uruchamiaj prototyp, a następnie iteruj go, dostosowując zachowanie symulowanego urządzenia na bieżąco. Ten proces pozwala udowodnić pomysł przed zainwestowanie w kosztowny sprzęt. Urządzenia niestandardowe można tworzyć za pomocą internetowego interfejsu użytkownika w celu wygenerowania prototypowego urządzenia w ciągu kilku sekund.
* Sprawdź, czy rozwiązanie działa zgodnie z oczekiwaniami od urządzenia do rozwiązania, symulując rzeczywiste zachowania urządzeń. Za pomocą języka JavaScript można tworzyć skrypty złożonych zachowań urządzeń w celu generowania realistycznej symulowanej telemetrii.
* Przetestuj swoje rozwiązanie, symulując normalne, szczytowe i poza szczytowe warunki obciążenia. Testy skalowania pomagają również w odpowiednim rozmiarze zasobów platformy Azure wymaganych do uruchomienia rozwiązania.

![Przykładowa symulacja drona](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Symulacja urządzenia umożliwia definiowanie modeli urządzeń w celu symulowania rzeczywistych urządzeń. Ten model zawiera formaty komunikatów, właściwości bliźniaczych reprezentacji i metody. Za pomocą języka JavaScript można również symulować złożone zachowania urządzeń.

Symulacje można uruchamiać dla jednego do tysięcy urządzeń łączących się z dowolnym centrum IoT. Aby ułatwić testowanie, możesz opcjonalnie wdrożyć centrum IoT wraz z symulacją urządzenia w środowisku autonomicznym.

Symulacja urządzenia jest bezpłatna. Jednak symulacja urządzenia jest wdrażana w subskrypcji platformy Azure w chmurze i zużywa zasoby platformy Azure. Jeśli symulacja urządzenia nie spełnia Twoich wymagań, kod źródłowy jest również dostępny w witrynie [GitHub](https://github.com/Azure/azure-iot-pcs-device-simulation) do kopiowania i modyfikowania.

## <a name="sample-simulations"></a>Przykładowe symulacje

Podczas wdrażania symulacji urządzenia otrzymasz kilka przykładowych symulacji i przykładowych urządzeń. Możesz użyć tych przykładów, aby dowiedzieć się, jak używać symulacji urządzenia. Aby rozpocząć, uruchom [przykładową symulację](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md). Możesz również utworzyć [własną symulację przy użyciu jednego z wielu przykładowych urządzeń.](iot-accelerators-device-simulation-create-simulation.md)

![Konfiguracja symulacji](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Niestandardowe symulowane urządzenia

Symulacja urządzenia umożliwia tworzenie [niestandardowych modeli urządzeń](iot-accelerators-device-simulation-create-custom-device.md) do użycia w symulacjach. Można na przykład zdefiniować nowy model urządzenia chłodziarki, który wysyła dane telemetryczne temperatury i wilgotności. Niestandardowe symulowane urządzenia idealnie nadają się do prostych zachowań urządzeń z losowymi, inkrementacjami lub dekrementacjami wartości telemetrycznych.

![Tworzenie modelu urządzenia](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Zaawansowane symulowane urządzenia

Jeśli potrzebujesz większej kontroli nad wartościami telemetrii, które wysyła urządzenie, możesz użyć zaawansowanego modelu urządzenia. Zaawansowane modele urządzeń umożliwiają obsługę języka JavaScript w celu manipulowania wysyłanymi wartościami telemetrii. Można na przykład zasymulować wewnętrzną temperaturę samochodu w gorącym, słoneczny dzień — w przypadku wzrostu temperatury wewnętrznej temperatura wewnętrzna rośnie wykładniczo.

Zaawansowane modele urządzeń umożliwiają tworzenie [i przekazywanie](iot-accelerators-device-simulation-advanced-device.md) własnych modeli urządzeń, które składają się z pliku definicji urządzenia JSON i odpowiadających im plików JavaScript.

Zaawansowane modele urządzeń umożliwiają:

* Określ format komunikatów wysyłanych z urządzenia wraz z typami telemetrii.
* Użyj niestandardowych skryptów do generowania wartości telemetrycznych, które utrzymują stan urządzenia w czasie.
* Użyj skryptów niestandardowych, aby określić, jak symulowane urządzenie reaguje na metody.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o akceleratorze rozwiązania do symulacji urządzeń i jego możliwościach. Aby wdrożyć akcelerator rozwiązań, odwiedź repozytorium GitHub:

> [!div class="nextstepaction"]
> [wdrażanie i uruchamianie symulacji urządzenia IoT na platformie Azure](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md)
