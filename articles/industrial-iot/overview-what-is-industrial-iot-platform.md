---
title: Platforma Azure Industrial IoT
description: Ten artykuł zawiera omówienie platformy IoT i jej składników.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 9908c020fb987e951f150fa27bce7c1eba973f7b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787932"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Co to jest platforma przemysłowa IoT (IIoT)?

Platforma Azure Industrial IoT to pakiet modułów i usług firmy Microsoft wdrożonych na platformie Azure. Te moduły i usługi mają w pełni wykorzystaną otwartość. W odniesieniu do programu firma Microsoft stosuje oferty zarządzanej platformy jako usługi (PaaS) platformy Azure, licencjonowane oprogramowanie typu "open source" w ramach licencji MIT, otwieranie międzynarodowych standardów komunikacji (OPC UA, AMQP, MQTT, HTTP) i interfejsów (Open API) oraz otwartych modeli danych przemysłowych (OPC UA) na brzegach i w chmurze.

## <a name="enabling-shopfloor-connectivity"></a>Włączanie łączności produkcyjnej 

Platforma Azure Industrial IoT obejmuje łączność przemysłową zasobów produkcyjnej (w tym odnajdywanie zasobów z obsługą OPC UA), normalizuje dane do formatu OPC UA i przesyła dane telemetryczne zasobów do platformy Azure w formacie OPC UA PubSub. W tym miejscu dane telemetryczne są przechowywane w bazie danych w chmurze. Ponadto platforma umożliwia bezpieczny dostęp do zasobów produkcyjnej za pośrednictwem OPC UA z chmury. Funkcje zarządzania urządzeniami (w tym Konfiguracja zabezpieczeń) również są wbudowane. Funkcja OPC UA została skompilowana przy użyciu technologii kontenera Docker w celu łatwego wdrażania i zarządzania. W przypadku zasobów, które nie są OPC UA, współpracujemy z wiodącymi dostawcami łączności przemysłowej i pomogą Ci przenieść oprogramowanie karty OPC UA do Azure IoT Edge. Te karty są dostępne w portalu Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Przemysłowe składniki IoT: moduły IoT Edge i mikrousługi w chmurze

Usługi brzegowe są implementowane jako moduły Azure IoT Edge i uruchamiane lokalnie. Mikrousługi w chmurze są implementowane jako mikrousługi ASP.NET przy użyciu interfejsu REST i działają na zarządzanych usługach Kubernetes platformy Azure lub autonomicznie na Azure App Service. W przypadku usług brzegowych i w chmurze udostępniono wstępnie skompilowane kontenery platformy Docker w programie Microsoft Container Registry (MCR), usuwając ten krok dla klienta. Usługi brzegowe i w chmurze korzystają ze sobą i muszą być używane razem. Udostępniono również łatwe w użyciu skrypty wdrażania, które umożliwiają wdrożenie całej platformy przy użyciu jednego polecenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już platformę Azure Industrial IoT, możesz dowiedzieć się więcej na temat wydawcy OPC:

> [!div class="nextstepaction"]
> [Co to jest Wydawca OPC?](overview-what-is-opc-publisher.md)