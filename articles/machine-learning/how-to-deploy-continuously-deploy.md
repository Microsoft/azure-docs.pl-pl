---
title: Ciągłe wdrażanie modeli Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak ciągle wdrażać modele przy użyciu rozszerzenia Azure Machine Learning DevOps. Automatycznie sprawdzaj, czy są i wdrażaj nowe wersje modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 9de971639e22f9656ea75dc64993ac5881efbffb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609417"
---
# <a name="continuously-deploy-models"></a>Ciągłe wdrażanie modeli

W tym artykule pokazano, jak używać ciągłego wdrażania w usłudze Azure DevOps, aby automatycznie sprawdzać dostępność nowych wersji zarejestrowanych modeli i wypchnąć te nowe modele do środowiska produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że model został już zarejestrowany w obszarze roboczym Azure Machine Learning. Zapoznaj się z [tym samouczkiem](how-to-train-scikit-learn.md) , aby zapoznać się z przykładem szkoleń i rejestrowania modelu scikit.

## <a name="continuously-deploy-models"></a>Ciągłe wdrażanie modeli

Możesz ciągle wdrażać modele przy użyciu rozszerzenia Machine Learning dla [usługi Azure DevOps](https://azure.microsoft.com/services/devops/). Możesz użyć rozszerzenia Machine Learning dla usługi Azure DevOps, aby wyzwolić potok wdrożenia w przypadku zarejestrowania nowego modelu uczenia maszynowego w obszarze roboczym Azure Machine Learning.

1. Utwórz konto, aby uzyskać [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up), które zapewnia ciągłą integrację i dostarczanie aplikacji na dowolną platformę lub chmurę. (Należy zauważyć, że Azure Pipelines nie są takie same jak [potoki Machine Learning](concept-ml-pipelines.md#compare).)

1. [Utwórz projekt usługi Azure DevOps.](/azure/devops/organizations/projects/create-project)

1. Zainstaluj [rozszerzenie Machine Learning dla Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Użyj połączeń usługi, aby skonfigurować połączenie główne usługi z obszarem roboczym Azure Machine Learning, aby umożliwić dostęp do artefaktów. Przejdź do pozycji Ustawienia projektu, wybierz pozycję **połączenia usługi**, a następnie wybierz pozycję **Azure Resource Manager**:

    [![Wybierz Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na liście **poziom zakresu** wybierz pozycję **AzureMLWorkspace**, a następnie wprowadź resztę wartości:

    ![Wybierz AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Aby ciągle wdrożyć model uczenia maszynowego przy użyciu Azure Pipelines, w obszarze potoki wybierz pozycję **Zwolnij**. Dodaj nowy artefakt, a następnie wybierz artefakt **modelu usługi Azure** i utworzone wcześniej połączenie z usługą. Wybierz model i wersję, aby wyzwolić wdrożenie:

    [![Wybierz model Azure](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Włącz wyzwalacz modelu w artefaktie modelu. Po włączeniu wyzwalacza za każdym razem, gdy określona wersja (czyli Najnowsza wersja) tego modelu jest zarejestrowana w obszarze roboczym, zostanie wyzwolony potok wydania usługi Azure DevOps.

    [![Włączanie wyzwalacza modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższymi projektami w usłudze GitHub, aby uzyskać więcej przykładów ciągłego wdrażania dla modeli ML.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)