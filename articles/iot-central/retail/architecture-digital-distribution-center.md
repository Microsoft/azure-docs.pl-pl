---
title: Architektura IoT Central centrum dystrybucji cyfrowego | Microsoft Docs
description: Architektura szablonu aplikacji centrum dystrybucji cyfrowej dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: b2e6993cd8dc77ccb9b43a3d5aef7b25fd9d7250
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347119"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architektura IoT Central szablonu aplikacji Digital Distribution Center



Partnerzy i klienci mogą korzystać z szablonu aplikacji & następujące wskazówki umożliwiają tworzenie kompleksowych rozwiązań **centrum dystrybucji cyfrowej** .

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Zbiór czujników IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i agregowane szczegółowe dane do IoT Central
3. Dane są kierowane do żądanej usługi platformy Azure w celu manipulowania
4. Za pomocą usług systemu Azure, takich jak ASA lub Azure Functions, można ponownie formatować strumienie danych i wysyłać je do żądanych kont magazynu
5. Przetworzone dane są przechowywane w magazynie gorącym dla akcji niemal w czasie rzeczywistym lub w chłodnym magazynie w celu uzyskania dodatkowych ulepszeń szczegółowych, które opierają się na analizie ML lub partii. 
6. Logic Apps może służyć do zarządzania różnymi przepływami pracy w aplikacjach dla użytkowników końcowych

## <a name="details"></a>Szczegóły
Poniższa sekcja zawiera opis każdej części architektury koncepcyjnej

## <a name="video-cameras"></a>Kamery wideo 
Kamery wideo są głównymi czujnikami w tym cyfrowym, połączonym ekosystemem w skali przedsiębiorstwa. Zaawansowana usługa uczenia maszynowego i sztuczna inteligencja, która pozwala na przekształcanie wideo w dane strukturalne i przetwarzanie go na brzegu przed wysłaniem do chmury. Za pomocą kamer IP można przechwytywać obrazy, kompresować je w aparacie, a następnie wysyłać skompresowane dane za pośrednictwem usługi Edge COMPUTE dla potoku analizy wideo lub używać aparatów GigE wzrok do przechwytywania obrazów na czujniku, a następnie wysyłania tych obrazów bezpośrednio do Azure IoT Edge, które następnie kompresują przed przetwarzaniem w potoku analizy wideo. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
Obciążenia "aparaty jako czujniki" i "krawędzie" są zarządzane lokalnie przez Azure IoT Edge i strumień aparatu jest przetwarzany przez potok analizy. Potok przetwarzania wideo Analytics w Azure IoT Edge ma wiele korzyści, w tym krótszy czas odpowiedzi, zużycie o niskiej przepustowości, które powoduje małe opóźnienia na potrzeby szybkiego przetwarzania danych. Tylko najważniejsze metadane, szczegółowe informacje i akcje są wysyłane do chmury w celu uzyskania dalszych działań lub badań. 

## <a name="device-management-with-iot-central"></a>Zarządzanie urządzeniami za pomocą IoT Central 
Azure IoT Central to platforma programistyczna dla rozwiązań, która upraszcza & urządzeń IoT Azure IoT Edge łączności bramy, konfiguracji i zarządzania. Platforma znacznie zmniejsza obciążenie i koszty związane z zarządzaniem urządzeniami IoT, operacjami i powiązanymi rozwiązaniami. Klienci & partnerzy mogą tworzyć kompleksowe rozwiązania dla przedsiębiorstw, aby uzyskać pętlę do przesyłania opinii cyfrowych w centrach dystrybucji.

## <a name="business-insights-and-actions-using-data-egress"></a>Szczegółowe informacje biznesowe i akcje przy użyciu danych wyjściowych 
Platforma IoT Central zapewnia zaawansowane opcje rozszerzalności za poorednictwem ciągłego eksportowania danych (CDE) i interfejsów API. Szczegółowe informacje biznesowe, które opierają się na przetwarzaniu danych telemetrycznych lub nieprzetworzonej telemetrii, są zwykle eksportowane do preferowanych aplikacji biznesowych. Można to osiągnąć za poorednictwem elementu webhook, Service Bus, centrum zdarzeń lub magazynu obiektów BLOB do kompilowania, uczenia i wdrażania modeli uczenia maszynowego oraz do dodatkowego wzbogacania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak wdrożyć [szablon centrum dystrybucji cyfrowej](./tutorial-iot-central-digital-distribution-center.md)
* Dowiedz się więcej o [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../core/overview-iot-central.md)
