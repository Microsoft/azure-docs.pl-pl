---
title: Uruchom Azure Container Instances-analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdróż kontenery analizy tekstu w usłudze Azure Container instance i przetestuj je w przeglądarce internetowej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: f7dbb79e2d9009b00b21d4c12663d8e6c855a4c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88243755"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Wdróż kontener analiza tekstu do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [analiza tekstu][install-and-run-containers] Cognitive Services na platformie Azure [Container Instances][container-instances]. Ta procedura exemplifies tworzenie zasobu analiza tekstu, tworzenie skojarzonego obrazu analiza tonacji i możliwość wykonywania tej aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[analiza tekstu dla kondycji](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Następne kroki 

* Użyj więcej [kontenerów Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [Analiza tekstu połączonej usługi](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances