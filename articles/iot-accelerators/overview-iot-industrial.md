---
title: Omówienie usługi Azure Industrial IoT | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi IoT rzeczy. Wyjaśniono w nim informacje dotyczące połączonej fabryki, łączności fabryki i składników zabezpieczeń w programie IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3df10c9d7630e9db76994e8e508f30adb986e0d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281817"
---
# <a name="what-is-industrial-iot-iiot"></a>Co to jest przemysł IoT (IIoT)

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

IIoT jest przemysłem Internet rzeczyowym. IIoT zwiększa efektywność przemysłową dzięki zastosowaniu IoT w branży produkcyjnej. 

## <a name="improve-industrial-efficiencies"></a>Poprawa efektywności przemysłowej

Zwiększ produktywność operacyjną i zyskowność dzięki akceleratorowi rozwiązania połączonej fabryki. Łączenie i monitorowanie sprzętu i urządzeń przemysłowych w chmurze, w tym maszyn już działających na etapie fabryki. Analizuj dane IoT w celu uzyskania szczegółowych informacji, które pomogą zwiększyć wydajność całego piętra fabryki.

Skracaj czasochłonny proces uzyskiwania dostępu do fabryk fabryki przy użyciu sznurka OPC i skup się na czasie tworzenia rozwiązań IIoT. Usprawnij zarządzanie certyfikatami i integrację zasobów branżowych z magazynem OPC, aby mieć pewność, że łączność z zasobami jest zabezpieczona. Te mikrousługi zapewniają interfejs API podobny do użycia na [platformie Azure przemysłowych IoT](https://github.com/Azure/Industrial-IoT). Interfejs API usługi zapewnia kontrolę funkcjonalności modułu Edge. 

![Omówienie przemysłowego Internetu rzeczy](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Aby uzyskać więcej informacji na temat usług Azure Industrial IoT, zobacz [repozytorium](https://github.com/Azure/Industrial-IoT) GitHub i [dokumentację](https://azure.github.io/Industrial-IoT/).
Jeśli nie znasz sposobu działania modułów Azure IoT Edge, Rozpocznij od następujących artykułów:
- [Azure IoT Edge — informacje](../iot-edge/about-iot-edge.md)
- [Moduły Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Połączona fabryka

[Połączona fabryka](../iot-accelerators/iot-accelerators-connected-factory-features.md) to implementacja architektury referencyjnej usługi Azure Industrial IoT firmy Microsoft, którą można dostosować w celu spełnienia określonych wymagań firmy. Pełny kod rozwiązania to "open source" i dostępne w repozytorium z akceleratorem rozwiązania połączonej fabryki. Można go użyć jako punktu wyjścia dla produktu komercyjnego i wdrożyć wstępnie skompilowane rozwiązanie w ramach subskrypcji platformy Azure w ciągu kilku minut. 

## <a name="factory-floor-connectivity"></a>Łączność z fabryką

OPC bliźniaczy jest składnikiem IIoT, który automatyzuje odnajdywanie i rejestrowanie urządzeń oraz oferuje zdalne sterowanie urządzeniami przemysłowymi za poorednictwem interfejsów API REST. OPC bliźniaczy, używa Azure IoT Edge i IoT Hub do łączenia chmur i sieci fabryki. OPC bliźniaczy pozwala deweloperom IIoT skupić się na tworzeniu aplikacji IIoT bez konieczności bezpiecznego uzyskiwania dostępu do maszyn lokalnych.

## <a name="security"></a>Zabezpieczenia

Magazyn OPC to implementacja serwera wykrywania globalnego OPC UA (GDS), który umożliwia konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatu dla serwera OPC UA i aplikacji klienckich w chmurze. Magazyn OPC upraszcza implementację i konserwację bezpiecznej łączności zasobów w przestrzeni przemysłowej. Dzięki automatyzowaniu zarządzania certyfikatami magazyn OPC zwalnia operatorów fabryki z ręcznych i złożonych procesów skojarzonych z łącznością i zarządzaniem certyfikatami.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już masz wprowadzenie do przemysłu IoT i jego składników, Oto sugerowany następny krok:

[Co to jest bliźniacza reprezentacja OPC](overview-opc-twin.md)
