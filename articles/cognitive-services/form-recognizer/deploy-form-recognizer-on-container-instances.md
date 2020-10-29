---
title: Uruchom kontener aparatu rozpoznawania formularzy w Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener aparatu rozpoznawania formularzy w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913182"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdróż kontener aparatu rozpoznawania formularzy do Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Dowiedz się, jak wdrożyć kontener [aparatu rozpoznawania Cognitive Services formularzy](form-recognizer-container-howto.md) na platformie Azure [Container Instances](../../container-instances/index.yml). Ta procedura pokazuje tworzenie zasobu aparatu rozpoznawania formularza platformy Azure. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]