---
title: Uruchom kontener wykrywania anomalii w Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener wykrywania anomalii w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 975efa9fdaff9175317794d8b6df7afbfc73cba8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83585690"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Wdróż kontener wykrywania anomalii w Azure Container Instances

Dowiedz się, jak wdrożyć kontener [wykrywania anomalii](../anomaly-detector-container-howto.md) Cognitive Services na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu wykrywania anomalii. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [Instalowanie i uruchamianie kontenerów](../anomaly-detector-container-configuration.md) na potrzeby ściągania obrazu kontenera i uruchamiania kontenera
* Przegląd [konfigurowania kontenerów](../anomaly-detector-container-configuration.md) dla ustawień konfiguracji
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
