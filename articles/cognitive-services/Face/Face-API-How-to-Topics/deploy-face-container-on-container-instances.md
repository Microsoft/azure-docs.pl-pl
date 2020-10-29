---
title: Uruchom kontener kroju w Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener do usługi Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: a5354581be519172c498e57d25510f9fc5c0daa4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911260"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Wdróż kontener programu Front-Azure Container Instances

> [!IMPORTANT]
> Osiągnięto limit liczby użytkowników kontenera funkcji rozpoznawania twarzy. Obecnie nie akceptujemy nowych aplikacji dla kontenera funkcji rozpoznawania twarzy.

Dowiedz się, jak wdrożyć kontener Cognitive Services [kroju](../face-how-to-install-containers.md) na platformie Azure [Container Instances](../../../container-instances/index.yml). Ta procedura pokazuje tworzenie zasobu platformy Azure. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]