---
title: Koncepcje dotyczące architektury w usłudze Azure IoT Central — panel słoneczny | Microsoft Docs
description: W tym artykule przedstawiono najważniejsze pojęcia związane z architekturą aplikacji do monitorowania w panelu słonecznym IoT Central platformy Azure.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 00ef6cf6d4149f139876cb0c2d845133ba00157c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127510"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central — architektura aplikacji w panelu słonecznym




Ten artykuł zawiera omówienie architektury szablonu aplikacji monitorowanie paneli słonecznej. Na poniższym diagramie przedstawiono powszechnie używaną architekturę dla aplikacji panel słoneczny na platformie Azure przy użyciu platformy IoT Central.

> [!div class="mx-imgBorder"]
> ![Architektura miernika inteligentnego](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Niniejsza architektura zawiera następujące składniki. Niektóre aplikacje mogą nie wymagać każdego wymienionego składnika.

## <a name="solar-panels-and-connectivity"></a>Panele i łączność słoneczna 

Panele słoneczne są jednym z znaczących źródeł odnawialnego zużycia energii. W zależności od typu i konfiguracji panelu, można połączyć go przy użyciu bram lub innych urządzeń pośrednich i systemów własnościowych. Może być konieczne utworzenie IoT Central mostku urządzeń w celu połączenia urządzeń, które nie mogą być połączone bezpośrednio. IoT Central mostka urządzenia to rozwiązanie Open Source, w [tym miejscu](../core/howto-build-iotc-device-bridge.md)można znaleźć pełne szczegóły. 



## <a name="iot-central-platform"></a>IoT Central platformę
Azure IoT Central to platforma, która upraszcza tworzenie rozwiązania IoT oraz zmniejsza obciążenie i koszty związane z zarządzaniem IoT, operacjami i programowaniem. Dzięki IoT Central można łatwo łączyć i monitorować zasoby Internet rzeczy (IoT) oraz zarządzać nimi na dużą skalę. Po podłączeniu paneli słonecznych do IoT Central szablon aplikacji będzie używać wbudowanych funkcji, takich jak modele urządzeń, polecenia i pulpity nawigacyjne. Szablon aplikacji korzysta również z IoT Central magazynu dla scenariuszy ścieżki ciepłej, takich jak monitorowanie danych, analiza, reguły i wizualizacja w czasie rzeczywistym.


## <a name="extensibility-options-to-build-with-iot-central"></a>Opcje rozszerzalności do skompilowania przy użyciu IoT Central
Platforma IoT Central udostępnia dwie opcje rozszerzalności: ciągły eksport danych (CDE) i interfejsy API. Klienci i partnerzy mogą wybrać jedną z tych opcji, aby dostosować swoje rozwiązania do konkretnych potrzeb. Na przykład jeden z naszych partnerów skonfigurował CDE z Azure Data Lake Storage (ADLS). Korzystają one z ADLS do długoterminowego przechowywania danych i innych scenariuszy magazynu zimnej ścieżki, takich jak przetwarzanie wsadowe, inspekcje i raportowanie. 

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy już wiesz o architekturze, [Utwórz bezpłatnie aplikację panelu słonecznego](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Aby dowiedzieć się więcej na temat IoT Central, zobacz [omówienie IoT Central](../index.yml)