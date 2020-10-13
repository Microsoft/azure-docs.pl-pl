---
title: Wykrywanie obiektów i ruchu usługi Azure IoT Central Video Analytics | Microsoft Docs
description: Dowiedz się, jak skompilować aplikację IoT Central przy użyciu szablonu wideo Analiza obiektów i funkcji wykrywania ruchu w IoT Central. Ten szablon używa analizy filmów wideo na żywo i podłączonych kamer.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874292"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Analiza wideo — architektura aplikacji wykrywania obiektów i ruchu

Szablon aplikacja do **wykrywania filmów wideo — obiekt i ruch** umożliwia tworzenie rozwiązań IoT, które obejmują możliwości analizy filmów wideo na żywo.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagram przedstawiający elementy analizy wideo i składniki wykrywania ruchu.":::

Najważniejsze składniki rozwiązania do analizy wideo obejmują:

## <a name="live-video-analytics-lva"></a>Analiza wideo na żywo (LVA)

Usługa LVA udostępnia platformę umożliwiającą tworzenie inteligentnych aplikacji wideo, które rozciągają się na brzeg i chmurę. Platforma umożliwia tworzenie inteligentnych aplikacji wideo, które rozciągają się na brzeg i chmurę. Platforma oferuje możliwość przechwytywania, rejestrowania, analizowania wideo na żywo i publikowania wyników, które mogą być plikami wideo lub analizami wideo w usługach platformy Azure. Usługi platformy Azure mogą działać w chmurze lub na krawędzi. Platforma może służyć do ulepszania rozwiązań IoT dzięki analizie wideo.

Aby uzyskać więcej informacji, zobacz [Live Video Analytics](https://github.com/Azure/live-video-analytics) w serwisie GitHub.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge moduł bramy LVA

Moduł IoT Edge LVA Gateway tworzy wystąpienia kamer jako nowych urządzeń i łączy je bezpośrednio z IoT Central przy użyciu zestawu SDK klienta urządzenia IoT.

W tej implementacji odwołania urządzenia łączą się z rozwiązaniem przy użyciu kluczy symetrycznych z krawędzi. Aby uzyskać więcej informacji na temat łączności urządzeń, zobacz Nawiązywanie [połączenia z usługą Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Graf multimedialny

Program Media Graph pozwala określić miejsce, z którego ma być przechwytywane nośniki, jak je przetworzyć i gdzie mają być dostarczane wyniki. Można skonfigurować program Media Graph, łącząc składniki lub węzły w odpowiedni sposób. Aby uzyskać więcej informacji, zobacz [Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) w witrynie GitHub.

## <a name="next-steps"></a>Następne kroki

Sugerowany następny krok to Dowiedz się, jak [wdrożyć aplikację IoT Central przy użyciu szablonu wideo Analiza obiektów i funkcji wykrywania ruchu](tutorial-video-analytics-deploy.md).
