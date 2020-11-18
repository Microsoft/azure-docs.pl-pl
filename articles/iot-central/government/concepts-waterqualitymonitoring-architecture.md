---
title: Architektura referencyjna dla rozwiązania do monitorowania jakości wody skompilowanego za pomocą platformy Azure IoT Central | Microsoft Docs
description: Poznaj koncepcje dotyczące rozwiązania do monitorowania jakości wody opracowanego przy użyciu usługi Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1c4cc7f5440aca27be085c238179cd0ab20b226a
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874053"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Architektura referencyjna monitorowania jakości wody 



Rozwiązania do monitorowania jakości wody można skompilować za pomocą **szablonu aplikacji platformy Azure IoT Central** jako startowej aplikacji IoT. Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej wysokiego poziomu dotyczące tworzenia kompleksowych rozwiązań. 


![Architektura monitorowania jakości wody](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Pojęcia:

1. Urządzenia i łączność  
1. IoT Central 
2. Rozszerzalność i integracje
3. Aplikacje biznesowe

Przyjrzyjmy się kluczowym składnikom, które zwykle odgrywają część w rozwiązaniu do monitorowania jakości wody.

## <a name="devices-and-connectivity"></a>Urządzenia i łączność 
W tej sekcji odnosimy się do urządzeń używanych do monitorowania jakości wody lub monitorowania zużycia wody, zazwyczaj jako urządzeń inteligentnych. Urządzenia inteligentnej wody mogą być licznikami przepływów, monitorów jakości wody, inteligentne zawory, Detektory wycieków itd.

Urządzenia używane w rozwiązaniach Smart wodna zwykle są połączone za pomocą sieci rozległych (LPWAN) z niską przepustowością przez operatora sieci innej firmy. W przypadku tych typów urządzeń można korzystać z [mostka urządzenia usługi azure IoT Central](../core/howto-build-iotc-device-bridge.md) , aby wysyłać dane urządzenia do aplikacji IoT na platformie Azure IoT Central. Alternatywnie mogą istnieć bramy urządzeń z możliwością IP i mogą łączyć się bezpośrednio z IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central to platforma aplikacji IoT, która umożliwia szybkie rozpoczęcie pracy z rozwiązaniem IoT. Możesz Marka, dostosowywać i zintegrować rozwiązanie z usługami innych firm.
Po połączeniu urządzeń z urządzeniami inteligentnymi do IoT Central uzyskasz polecenie i kontrolę, monitorowanie i alerty, interfejs użytkownika z wbudowanymi RBAC, konfigurowalne pulpity nawigacyjne usługi Insights oraz opcje rozszerzalności. 

## <a name="extensibility-and-integrations"></a>Rozszerzalność i integracje
Aplikację IoT można zwiększyć w IoT Central i opcjonalnie:
* Przekształcaj i Integruj dane IoT na potrzeby zaawansowanej analizy, na przykład szkolenia modeli uczenia maszynowego za pośrednictwem ciągłego eksportu danych z aplikacji IoT Central
* Automatyzowanie przepływów pracy w innych systemach przez wyzwalanie akcji przy użyciu automatyzacji lub elementów webhook w aplikacji IoT Central
* programowo uzyskać dostęp do aplikacji IoT w IoT Central za poorednictwem IoT Central interfejsów API

## <a name="business-applications"></a>Aplikacje biznesowe 
Dane IoT mogą służyć do zarządzania różnymi aplikacjami biznesowymi w ramach narzędzia wodnego. Aby dowiedzieć się, jak IoT Central połączyć aplikację do monitorowania jakości wody z usługami pól, należy postępować zgodnie z artykułem na temat [integracji z usługami pól usługi Dynamics 365](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć IoT Central aplikacji do [monitorowania jakości wody](./tutorial-water-quality-monitoring.md)
* Dowiedz się więcej na temat [IoT Central szablonów dla instytucji rządowych](./overview-iot-central-government.md)
* Aby dowiedzieć się więcej na temat IoT Central, zobacz [omówienie IoT Central](../core/overview-iot-central.md)
