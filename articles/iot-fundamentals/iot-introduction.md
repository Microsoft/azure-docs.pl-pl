---
title: Wprowadzenie do usługi Azure Internet of Things (IoT)
description: Wprowadzenie z wyjaśnieniem podstaw usługi Azure IoT i usług IoT wraz z przykładami ułatwiającymi zilustrowanie zastosowań IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "81729006"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co to jest usługa Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) to kolekcja zarządzanych przez firmę Microsoft usług w chmurze, które łączą, monitorują i kontrolują miliardy zasobów Internetu rzeczy. W prostszej postaci rozwiązanie IoT składa się z co najmniej jednego urządzenia IoT, które komunikuje się z co najmniej jedną usługą zaplecza hostowaną w chmurze. 

## <a name="iot-devices"></a>Urządzenia IoT

Urządzenie IoT zwykle składa się z tablicy obwodowej z dołączanymi czujnikami, które używają sieci Wi-Fi do łączenia się z Internetem. Na przykład:

* Czujnik ciśnienia na zdalnej pompie naftowej.
* Czujniki temperatury i wilgotności w jednostce klimatyzacyjnej.
* Przyspieszeniomierz w wIndie.
* Czujniki obecności w pokoju.

Istnieje wiele różnych urządzeń dostępnych od różnych producentów do tworzenia rozwiązań. Aby uzyskać listę urządzeń certyfikowanych do pracy z usługą Azure IoT Hub, zobacz [wykaz urządzeń z certyfikatem platformy Azure dla IoT](https://catalog.azureiotsolutions.com/alldevices). Do tworzenia prototypów można użyć urządzeń, takich jak [zestawu deweloperskiego IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) lub [Raspberry Pi](https://www.raspberrypi.org/). Devkit ma wbudowane czujniki do temperatury, ciśnienia, wilgotności i żyroskop, przyspieszeniomierza i Magnetometer. Raspberry Pi umożliwia dołączenie wielu różnych typów czujników. 

Firma Microsoft udostępnia [zestawy SDK urządzeń](../iot-hub/iot-hub-devguide-sdks.md) Open Source, za pomocą których można tworzyć aplikacje działające na urządzeniach. Te [zestawy SDK upraszczają i przyspieszają](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) opracowywanie rozwiązań IoT.

## <a name="communication"></a>Komunikacja

Zazwyczaj urządzenia IoT wysyłają dane telemetryczne z czujników do usług zaplecza w chmurze. Jednak inne typy komunikacji mogą być takie jak usługa zaplecza do wysyłania poleceń do urządzeń. Poniżej przedstawiono kilka przykładów komunikacji między urządzeniem i chmurą oraz z chmurą i urządzeniem:

* Przenośna wózek chłodniczy wysyła do IoT Hub temperaturę co 5 minut. 

* Usługa zaplecza wysyła do urządzenia polecenie, aby zmienić częstotliwość wysyłania telemetrii w celu ułatwienia zdiagnozowania problemu. 

* Urządzenie wysyła alerty na podstawie wartości odczytywanych z czujników. Na przykład urządzenie monitorujące reaktor usługi Batch w zakładzie chemicznym wysyła alert, gdy temperatura przekracza określoną wartość.

* Urządzenia wysyłają informacje do wyświetlenia na pulpicie nawigacyjnym do wyświetlania przez operatorów ludzkich. Na przykład w potoku, w którym można określić temperaturę, ciśnienie i przepływy, umożliwia operatorom monitorowanie funkcji. 

[Zestawy SDK urządzeń IoT](../iot-hub/iot-hub-devguide-sdks.md) i IoT Hub obsługują typowe [Protokoły komunikacyjne](../iot-hub/iot-hub-devguide-protocols.md) , takie jak http, MQTT i AMQP.

Urządzenia IoT mają różne cechy w porównaniu z innymi klientami, takimi jak przeglądarki i aplikacje mobilne. Zestawy SDK urządzeń ułatwiają rozwiązywanie problemów związanych z bezpiecznym i niezawodnym połączeniem urządzeń z usługą zaplecza.  W szczególności urządzenia IoT:

* są często systemami osadzonymi bez osoby pełniącej rolę operatora (w odróżnieniu od telefonu);
* mogą być wdrażane w lokalizacjach zdalnych, gdzie dostęp fizyczny jest bardzo kosztowny;
* mogą być dostępne tylko za pośrednictwem zaplecza rozwiązania;
* mogą mieć ograniczone zasoby w zakresie zasilania i przetwarzania;
* mogą korzystać z przerywanej, powolnej lub kosztownej łączności sieciowej;
* mogą wymagać używania zastrzeżonych, niestandardowych lub branżowych protokołów aplikacji;

## <a name="back-end-services"></a>Usługi zaplecza 

W rozwiązaniu IoT usługa zaplecza udostępnia funkcje takie jak:

* Odbieranie telemetrii z urządzeń we właściwej skali i określanie sposobu przetwarzania i przechowywania tych danych.
* Analizowanie danych telemetrycznych w celu udostępnienia szczegółowych informacji, w czasie rzeczywistym lub po fakcie.
* Wysyłanie poleceń z chmury do określonego urządzenia. 
* Inicjowanie obsługi administracyjnej urządzeń i sterowanie urządzeniami, które mogą łączyć się z infrastrukturą.
* Kontrolowanie stanu urządzeń i monitorowanie ich aktywności.
* Zarządzanie oprogramowaniem układowym zainstalowanym na urządzeniach.

Na przykład w rozwiązaniu do zdalnego monitorowania stacji dokującej, zaplecze w chmurze używa danych telemetrycznych z pomp do identyfikacji nietypowego zachowania. Gdy usługa zaplecza zidentyfikuje anomalię, może automatycznie wysłać polecenie z powrotem do urządzenia w celu podjęcia działań naprawczych. Ten proces generuje pętlę automatycznego sprzężenia zwrotnego między urządzeniem i chmurą, która znacznie zwiększa wydajność rozwiązania.

## <a name="azure-iot-examples"></a>Przykłady usługi Azure IoT

Aby zapoznać się z przykładami dotyczącymi sposobu korzystania z usługi Azure IoT przez organizacje, zobacz [techniczne analizy przypadków firmy Microsoft dla IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="next-steps"></a>Następne kroki

Rzeczywiste przypadki zastosowań biznesowych i opisy używanej architektury można znaleźć na stronie [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Techniczne analizy przypadków usługi Microsoft Azure IoT).

W [katalogu projektów IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) można znaleźć kilka przykładowych projektów do wypróbowania z układem IoT DevKit. 

Aby uzyskać bardziej szczegółowy opis różnych usług i sposobu ich używania, zobacz [usługi i technologie Azure IoT](iot-services-and-technologies.md).

Szczegółowe omówienie architektury IoT można znaleźć w temacie [Architektura referencyjna IoT platformy Microsoft Azure](https://aka.ms/iotrefarchitecture).
