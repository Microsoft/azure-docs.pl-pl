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
ms.openlocfilehash: de81fcb5ee62f9b295c93132b271507c040af46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512790"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdróż kontener aparatu rozpoznawania formularzy do Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Dowiedz się, jak wdrożyć kontener [aparatu rozpoznawania Cognitive Services formularzy](form-recognizer-container-howto.md) na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu aparatu rozpoznawania formularza platformy Azure. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
