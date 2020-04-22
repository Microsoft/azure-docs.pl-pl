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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729006"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co to jest usługa Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) to kolekcja zarządzanych przez firmę Microsoft usług w chmurze, które łączą, monitorują i kontrolują miliardy zasobów Internetu rzeczy. W prostszych słowach rozwiązanie IoT składa się z jednego lub więcej urządzeń IoT, które komunikują się z jedną lub kilkoma usługami zaplecza hostowanymi w chmurze. 

## <a name="iot-devices"></a>Urządzenia IoT

Urządzenie IoT składa się zazwyczaj z płytki drukowanej z podłączonymi czujnikami, które używają WiFi do łączenia się z Internetem. Przykład:

* Czujnik ciśnienia na zdalnej pompie olejowej.
* Czujniki temperatury i wilgotności w jednostce klimatyzacyjnej.
* Akcelerometr w windzie.
* Czujniki obecności w pomieszczeniu.

Istnieje wiele różnych urządzeń dostępnych od różnych producentów, aby zbudować rozwiązanie. Aby uzyskać listę urządzeń certyfikowanych do pracy z usługą Azure IoT Hub, zobacz [katalog urządzeń z certyfikatem platformy Azure dla IoT.](https://catalog.azureiotsolutions.com/alldevices) Do tworzenia prototypów można używać urządzeń, takich jak [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) lub [Raspberry Pi](https://www.raspberrypi.org/). Devkit posiada wbudowane czujniki temperatury, ciśnienia, wilgotności i żyroskopu, akcelerometru i magnetometru. Raspberry Pi umożliwia podłączenie wielu różnych typów czujników. 

Firma Microsoft udostępnia [pakiety SDK urządzeń](../iot-hub/iot-hub-devguide-sdks.md) typu open source, których można używać do tworzenia aplikacji uruchamianych na urządzeniach. Te [SDK upraszczają i przyspieszają](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) opracowywanie rozwiązań IoT.

## <a name="communication"></a>Komunikacja

Zazwyczaj urządzenia IoT wysyłają dane telemetryczne z czujników do usług zaplecza w chmurze. Jednak inne typy komunikacji są możliwe, takie jak usługa zaplecza wysyłanie poleceń do urządzeń. Oto kilka przykładów komunikacji między urządzeniami a chmurą i chmury z urządzeniem:

* Mobilna ciężarówka chłodnicza wysyła temperaturę co 5 minut do centrum IoT Hub. 

* Usługa zaplecza wysyła polecenie do urządzenia, aby zmienić częstotliwość, z jaką wysyła dane telemetryczne, aby pomóc zdiagnozować problem. 

* Urządzenie wysyła alerty na podstawie wartości odczytanych z jego czujników. Na przykład urządzenie monitorujące reaktor wsadowy w zakładzie chemicznym wysyła alert, gdy temperatura przekracza określoną wartość.

* Urządzenia wysyłają informacje do wyświetlenia na pulpicie nawigacyjnym w celu wyświetlania przez operatorów ludzkich. Na przykład pomieszczenie sterownicza w rafinerii może wykazywać temperaturę, ciśnienie i natężenie przepływu w każdej rurze, umożliwiając operatorom monitorowanie obiektu. 

Moduły [SDK urządzeń IoT](../iot-hub/iot-hub-devguide-sdks.md) i Centrum IoT obsługują typowe [protokoły komunikacyjne,](../iot-hub/iot-hub-devguide-protocols.md) takie jak HTTP, MQTT i AMQP.

Urządzenia IoT mają różne cechy w porównaniu z innymi klientami, takimi jak przeglądarki i aplikacje mobilne. SDK urządzeń pomagają w rozwiązywaniu problemów związanych z bezpiecznym i niezawodnym łączeniem urządzeń z usługą zaplecza.  W szczególności urządzenia IoT:

* są często systemami osadzonymi bez osoby pełniącej rolę operatora (w odróżnieniu od telefonu);
* mogą być wdrażane w lokalizacjach zdalnych, gdzie dostęp fizyczny jest bardzo kosztowny;
* mogą być dostępne tylko za pośrednictwem zaplecza rozwiązania;
* mogą mieć ograniczone zasoby w zakresie zasilania i przetwarzania;
* mogą korzystać z przerywanej, powolnej lub kosztownej łączności sieciowej;
* mogą wymagać używania zastrzeżonych, niestandardowych lub branżowych protokołów aplikacji;

## <a name="back-end-services"></a>Usługi zaplecza 

W rozwiązaniu IoT usługa zaplecza zapewnia funkcje, takie jak:

* Odbieranie telemetrii z urządzeń we właściwej skali i określanie sposobu przetwarzania i przechowywania tych danych.
* Analizowanie danych telemetrycznych w celu udostępnienia szczegółowych informacji, w czasie rzeczywistym lub po fakcie.
* Wysyłanie poleceń z chmury do określonego urządzenia. 
* Inicjowanie obsługi administracyjnej urządzeń i kontrolowanie, które urządzenia mogą łączyć się z infrastrukturą.
* Kontrolowanie stanu urządzeń i monitorowanie ich aktywności.
* Zarządzanie oprogramowaniem układowym zainstalowanym na urządzeniach.

Na przykład w rozwiązaniu zdalnego monitorowania dla przepompowni oleju zaplecza chmury używa danych telemetrycznych z pomp do identyfikowania nietypowe zachowanie. Gdy usługa zaplecza identyfikuje anomalię, może automatycznie wysłać polecenie z powrotem do urządzenia, aby podjąć działania naprawcze. Ten proces generuje pętlę automatycznego sprzężenia zwrotnego między urządzeniem i chmurą, która znacznie zwiększa wydajność rozwiązania.

## <a name="azure-iot-examples"></a>Przykłady usługi Azure IoT

Aby uzyskać rzeczywiste przykłady sposobu, w jaki organizacje korzystają z usługi Azure IoT, zobacz [Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="next-steps"></a>Następne kroki

Rzeczywiste przypadki zastosowań biznesowych i opisy używanej architektury można znaleźć na stronie [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Techniczne analizy przypadków usługi Microsoft Azure IoT).

W [katalogu projektów IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) można znaleźć kilka przykładowych projektów do wypróbowania z układem IoT DevKit. 

Aby uzyskać bardziej kompleksowe wyjaśnienie różnych usług i sposobu ich wykorzystania, zobacz [usługi i technologie Azure IoT.](iot-services-and-technologies.md)

Szczegółowe omówienie architektury IoT można znaleźć w temacie [Architektura referencyjna IoT platformy Microsoft Azure](https://aka.ms/iotrefarchitecture).
