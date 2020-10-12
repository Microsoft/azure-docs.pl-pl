---
title: Uruchom usługę Azure Kubernetes Service — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdróż obraz kontenera analiza tekstu w usłudze Azure Kubernetes i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b7a5953edd9aec96a7f75e747c39e8f07f7210bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88243772"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Wdrażanie kontenera analiza tekstu w usłudze Azure Kubernetes Service

Dowiedz się, jak wdrożyć obraz kontenera [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) usługi Azure Cognitive Services w usłudze Azure Kubernetes Service (AKS). W tej procedurze pokazano, jak utworzyć zasób analiza tekstu, sposób tworzenia skojarzonego obrazu analizy tonacji oraz jak korzystać z tej aranżacji dwóch z przeglądarki. Korzystanie z kontenerów może przesunąć uwagę na zarządzanie infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchomione lokalnie. Nie używaj Azure Cloud Shell. Potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services).
* Edytor tekstu, na przykład [Visual Studio Code](https://code.visualstudio.com/download).
* Zainstalowano [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .
* Zainstalowano [interfejs wiersza polecenia Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) .
* Zasób platformy Azure z poprawną warstwą cenową. Nie wszystkie warstwy cenowe pracują z tym kontenerem:
    * Zasób **platformy Azure analiza tekstu** tylko przy użyciu warstw cenowych F0 lub standard.
    * Zasób **Cognitive Services platformy Azure** z warstwą cenową S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Następne kroki

* Użyj więcej [kontenerów Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [Analiza tekstu połączonej usługi](../vs-text-connected-service.md)
