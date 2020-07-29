---
title: Eksportowanie lub usuwanie danych obszaru roboczego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyeksportować lub usunąć obszar roboczy za pomocą interfejsów API platformy Azure Machine Learning Studio, interfejsu wiersza polecenia, zestawu SDK i uwierzytelniane.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 657f6dd32c18b5b0745883da02563e9f5257d92d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87307220"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Eksportowanie lub usuwanie danych obszaru roboczego usługi Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W Azure Machine Learning można eksportować lub usuwać dane obszaru roboczego za pomocą uwierzytelnionego interfejsu API REST. Ten artykuł zawiera informacje o tym, jak to zrobić.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrolowanie danych obszaru roboczego

Dane w produkcie przechowywane przez Azure Machine Learning są dostępne do eksportowania i usuwania za pomocą Azure Machine Learning Studio, interfejsu wiersza polecenia, zestawu SDK i uwierzytelnionych interfejsów API REST. Dostęp do danych telemetrycznych można uzyskać za pomocą portalu ochrony prywatności systemu Azure. 

W Azure Machine Learning dane osobowe składają się z informacji o użytkowniku w dokumentach historia uruchamiania i rekordy telemetrii niektórych interakcji użytkownika z usługą.

## <a name="delete-workspace-data-with-the-rest-api"></a>Usuwanie danych obszaru roboczego za pomocą interfejsu API REST

W celu usunięcia danych można wykonać następujące wywołania interfejsu API z czasownikiem HTTP DELETE. Są one autoryzowane przez posiadanie `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla `https://management.core.windows.net/` punktu końcowego.  

Aby dowiedzieć się, jak uzyskać ten token i wywoływać punkty końcowe platformy Azure, zobacz temat [Używanie REST do zarządzania zasobami ml](how-to-manage-rest.md) i [dokumentacją interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/).  

W poniższych przykładach Zastąp tekst w {} nazwach wystąpień, które określają skojarzony zasób.

### <a name="delete-an-entire-workspace"></a>Usuwanie całego obszaru roboczego

To wywołanie służy do usuwania całego obszaru roboczego.  
> [!WARNING]
> Wszystkie informacje zostaną usunięte, a obszar roboczy nie będzie już używany.

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="delete-models"></a>Usuwanie modeli

To wywołanie umożliwia uzyskanie listy modeli i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Poszczególne modele można usuwać za pomocą:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="delete-assets"></a>Usuń zasoby

Użyj tego wywołania, aby uzyskać listę zasobów i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Poszczególne zasoby można usuwać za pomocą:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="delete-images"></a>Usuwanie obrazów

Użyj tego wywołania, aby uzyskać listę obrazów i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Poszczególne obrazy można usuwać za pomocą:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="delete-services"></a>Usuń usługi

Użyj tego wywołania, aby uzyskać listę usług i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Poszczególne usługi można usuwać z:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

## <a name="export-service-data-with-the-rest-api"></a>Eksportowanie danych usługi za pomocą interfejsu API REST

W celu wyeksportowania danych można wykonać następujące wywołania interfejsu API z czasownikiem HTTP GET. Są one autoryzowane przez posiadanie `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla punktu końcowego`https://management.core.windows.net/`  

Aby dowiedzieć się, jak uzyskać ten token i wywoływać punkty końcowe platformy Azure, zobacz temat [Używanie REST do zarządzania zasobami ml](how-to-manage-rest.md) i [dokumentacją interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/).   

W poniższych przykładach Zastąp tekst w {} nazwach wystąpień, które określają skojarzony zasób.

### <a name="export-workspace-information"></a>Eksportuj informacje o obszarze roboczym

Użyj tego wywołania, aby uzyskać listę wszystkich obszarów roboczych:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01`

Informacje o poszczególnych obszarach roboczych można uzyskać przez:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="export-compute-information"></a>Eksportowanie informacji obliczeniowych

Wszystkie obiekty docelowe obliczeń dołączone do obszaru roboczego można uzyskać przez:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01`

Informacje o pojedynczym miejscu docelowym obliczeń można uzyskać przez:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01`

### <a name="export-run-history-data"></a>Eksportuj dane historii przebiegów

Użyj tego wywołania, aby uzyskać listę wszystkich eksperymentów i ich informacji:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments`

Wszystkie uruchomienia dla konkretnego eksperymentu można uzyskać, wykonując następujące działania:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs`

Elementy historii uruchamiania mogą być uzyskiwane przez:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId}`

Wszystkie metryki uruchamiania dla eksperymentu można uzyskać, wykonując następujące działania:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics`

Pojedynczą metrykę przebiegu można uzyskać przez:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}`

### <a name="export-artifacts"></a>Eksportuj artefakty

Użyj tego wywołania, aby uzyskać listę artefaktów i ich ścieżek:

`https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}`

### <a name="export-notifications"></a>Eksportuj powiadomienia

Użyj tego wywołania, aby uzyskać listę przechowywanych zadań:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks`

Powiadomienia dla pojedynczego zadania mogą zostać uzyskane przez:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}`

### <a name="export-data-stores"></a>Eksportowanie magazynów danych

Użyj tego wywołania, aby uzyskać listę magazynów danych:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores`

Pojedyncze magazyny danych można uzyskać przez:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}`

### <a name="export-models"></a>Eksportowanie modeli

To wywołanie umożliwia uzyskanie listy modeli i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Poszczególne modele są uzyskiwane przez:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="export-assets"></a>Eksportuj zasoby

Użyj tego wywołania, aby uzyskać listę zasobów i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Poszczególne zasoby mogą być uzyskiwane przez:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="export-images"></a>Eksportuj obrazy

Użyj tego wywołania, aby uzyskać listę obrazów i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Poszczególne obrazy można uzyskać przez:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="export-services"></a>Eksportowanie usług

Użyj tego wywołania, aby uzyskać listę usług i ich identyfikatorów:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Poszczególne usługi mogą być uzyskiwane przez:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

### <a name="export-pipeline-experiments"></a>Wyeksportuj eksperymenty potoku

Poszczególne eksperymenty mogą być uzyskiwane przez:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}`

### <a name="export-pipeline-graphs"></a>Eksportuj wykresy potoku

Poszczególne wykresy mogą być uzyskiwane przez:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}`

### <a name="export-pipeline-modules"></a>Eksportowanie modułów potoku

Moduły mogą być uzyskiwane przez:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}`

### <a name="export-pipeline-templates"></a>Eksportowanie szablonów potoku

Szablony mogą być uzyskiwane przez:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}`

### <a name="export-pipeline-data-sources"></a>Eksportuj źródła danych potoku

Źródła danych można uzyskać przez:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}`

## <a name="delete-assets-in-the-designer"></a>Usuwanie zasobów w projektancie

W projektancie, w którym został utworzony eksperyment, Usuń pojedyncze zasoby:

1. Przejdź do projektanta

    ![Usuń zasoby](./media/how-to-export-delete-data/delete-experiment.png)

1. Z listy wybierz wersję roboczą pojedynczego potoku do usunięcia.

1. Wybierz pozycję **Usuń**.

### <a name="delete-datasets-in-the-designer"></a>Usuwanie zestawów danych w projektancie

Aby usunąć zestawy danych w projektancie, użyj Azure Portal lub Eksplorator usługi Storage, aby przejść do połączonych kont magazynu i usunąć zestawy danych. Wyrejestrowanie zestawów danych w projektancie powoduje jedynie usunięcie punktu odniesienia w magazynie.

