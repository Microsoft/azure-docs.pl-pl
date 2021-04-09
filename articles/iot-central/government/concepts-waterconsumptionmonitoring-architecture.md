---
title: Architektura referencyjna dla rozwiązania do monitorowania zużycia wody, utworzonego za pomocą platformy Azure IoT Central | Microsoft Docs
description: Poznaj koncepcje dotyczące rozwiązania do monitorowania zużycia wody opracowanego przy użyciu usługi Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831658"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Architektura referencyjna monitorowania zużycia wody 

Rozwiązania do monitorowania zużycia wody można skompilować za pomocą **szablonu aplikacji platformy Azure IoT Central** jako startowej aplikacji IoT. Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej wysokiego poziomu dotyczące tworzenia kompleksowych rozwiązań. 

![Architektura monitorowania zużycia wody](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Pojęcia:

1. Urządzenia i łączność  
1. IoT Central 
1. Rozszerzalność i integracje
1. Aplikacje biznesowe

Przyjrzyjmy się kluczowym składnikom, które zwykle odgrywają część w rozwiązaniu do monitorowania zużycia wody.

## <a name="devices-and-connectivity"></a>Urządzenia i łączność 
Ta sekcja odnosi się do urządzeń używanych do elektronicznych rozwiązań wodnych, takich jak monitorowanie jakości wody lub monitorowanie zużycia wody, zazwyczaj jako urządzenia inteligentnej wody. Urządzenia inteligentnej wody mogą być licznikami przepływów, monitorów jakości wody, inteligentne zawory, Detektory wycieków itd.

Urządzenia używane w rozwiązaniach Smart wodna zwykle są połączone za pomocą sieci rozległych (LPWAN) z niską przepustowością przez operatora sieci innej firmy. W przypadku tych typów urządzeń możesz użyć [mostka urządzenia usługi Azure IoT Central](../core/howto-build-iotc-device-bridge.md) , aby wysłać dane urządzenia do aplikacji IoT na platformie Azure IoT Central. Alternatywnie mogą istnieć bramy urządzeń z możliwością IP i mogą łączyć się bezpośrednio z IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central to platforma aplikacji IoT, która umożliwia szybkie rozpoczęcie pracy z rozwiązaniem IoT. Możesz Marka, dostosowywać i zintegrować rozwiązanie z usługami innych firm.
Po połączeniu urządzeń z urządzeniami inteligentnymi do IoT Central uzyskasz polecenie i kontrolę, monitorowanie i alerty, interfejs użytkownika z wbudowanymi RBAC, konfigurowalne pulpity nawigacyjne usługi Insights oraz opcje rozszerzalności. 


## <a name="extensibility-and-integrations"></a>Rozszerzalność i integracje
Aplikację IoT można zwiększyć w IoT Central i opcjonalnie:
* Przekształcaj i Integruj dane IoT na potrzeby zaawansowanej analizy, na przykład szkolenia modeli uczenia maszynowego za pośrednictwem ciągłego eksportu danych z aplikacji IoT Central
* Automatyzowanie przepływów pracy w innych systemach przez wyzwalanie akcji przy użyciu automatyzacji lub elementów webhook w aplikacji IoT Central
* programowo uzyskać dostęp do aplikacji IoT w IoT Central za poorednictwem IoT Central interfejsów API

## <a name="business-applications"></a>Aplikacje biznesowe 
Dane IoT mogą służyć do zarządzania różnymi rodzajami aplikacji firmowych w ramach narzędzia wodnego. Aby dowiedzieć się, jak połączyć aplikację monitorowania zużycia wody IoT Central z usługami pól, postępuj zgodnie z samouczkiem dotyczącym [integracji z usługami pól usługi Dynamics 365](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć IoT Centralową aplikację do [wykorzystania wody](./tutorial-water-consumption-monitoring.md)
* Dowiedz się więcej na temat [IoT Central szablonów dla instytucji rządowych](./overview-iot-central-government.md)
* Aby dowiedzieć się więcej na temat IoT Central, zobacz [omówienie IoT Central](../core/overview-iot-central.md)
