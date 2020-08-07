---
title: Jak i gdzie wdrażać modele
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrażać modele Azure Machine Learning, w tym Azure Container Instances, Kubernetes usługi Azure, Azure IoT Edge i programowalne pola tablic bram.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 5e73744f3d467d08944d0e8800dd6d8824857abd
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846993"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Wdrażanie modeli za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model uczenia maszynowego jako usługę sieci Web w chmurze platformy Azure lub Azure IoT Edge urządzeń.

Przepływ pracy wygląda podobnie niezależnie od tego, gdzie wdrażasz model:

1. Zarejestrowanie modelu.
1. Przygotuj konfigurację wnioskowania
1. Przygotowywanie skryptu wprowadzania (chyba że jest używane [wdrożenie bez kodu](how-to-deploy-no-code-deployment.md))
1. Wdrożenie modelu na docelowym obiekcie obliczeniowym.
1. Przetestuj wdrożony model, nazywany również usługą sieci Web.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrożenia, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](concept-model-management-and-deployment.md).


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>Informacje o stanie usługi

Podczas wdrażania modelu może zostać wyświetlona zmiana stanu usługi podczas jej pełnego wdrożenia.

W poniższej tabeli opisano różne stany usług:

| Stan usługi WebService | Opis | Końcowy stan?
| ----- | ----- | ----- |
| Przechodzenie | Usługa jest w trakcie wdrażania. | Nie |
| Nieprawidłowy | Usługa została wdrożona, ale jest obecnie nieosiągalna.  | Nie |
| Unschedulable | Nie można teraz wdrożyć usługi z powodu braku zasobów. | Nie |
| Niepowodzenie | Wdrożenie usługi nie powiodło się z powodu błędu lub awarii. | Tak |
| Dobra kondycja | Usługa jest w dobrej kondycji, a punkt końcowy jest dostępny. | Tak |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Wnioskowanie partii
Azure Machine Learning obiekty docelowe obliczeń są tworzone i zarządzane przez Azure Machine Learning. Mogą one służyć do prognozowania partii z potoków Azure Machine Learning.

Aby zapoznać się z przewodnikiem dotyczącym wnioskowania partii przy użyciu obliczeń Azure Machine Learning, zobacz [jak uruchamiać przewidywania wsadowe](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge wnioskowanie
Obsługa wdrażania na brzegu jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Machine Learning jako moduł IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Użyj protokołu TLS do zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)

