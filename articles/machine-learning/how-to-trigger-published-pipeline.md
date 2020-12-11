---
title: Wyzwól potoki ML dla nowych danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyzwolić uruchomienie potoku Azure Machine Learning przy użyciu Azure Logic Apps w celu reagowania na nowe dane.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4
ms.openlocfilehash: 37a18d147d3aca713d0c6bd934e23aa22b2521a5
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028890"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Wyzwalanie przebiegu potoku Machine Learning z poziomu aplikacji logiki

Wyzwalaj uruchomienie potoku Azure Machine Learning, gdy pojawią się nowe dane. Na przykład możesz chcieć wyzwolić potok, aby szkolić nowy model, gdy nowe dane pojawią się na koncie usługi BLOB Storage. Skonfiguruj wyzwalacz przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Punkt końcowy REST dla opublikowanego potoku Machine Learning. [Tworzenie i publikowanie potoku](how-to-create-your-first-pipeline.md). Następnie Znajdź punkt końcowy REST PublishedPipeline przy użyciu identyfikatora potoku:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Magazyn obiektów blob platformy Azure](../storage/blobs/storage-blobs-overview.md) do przechowywania danych.
* [Magazyn](how-to-access-data.md) danych w obszarze roboczym zawierający szczegóły konta magazynu obiektów BLOB.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Teraz Utwórz wystąpienie [aplikacji logiki platformy Azure](../logic-apps/logic-apps-overview.md) . Jeśli chcesz, [Użyj środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) i [Skonfiguruj klucz zarządzany przez klienta](../logic-apps/customer-managed-keys-integration-service-environment.md) do użycia przez aplikację logiki.

Po udostępnieniu aplikacji logiki wykonaj następujące kroki, aby skonfigurować wyzwalacz dla potoku:

1. [Utwórz tożsamość zarządzaną przypisaną przez system](../logic-apps/create-managed-service-identity.md) , aby umożliwić aplikacji dostęp do obszar roboczy usługi Azure Machine Learning.

1. Przejdź do widoku projektanta aplikacji logiki i wybierz szablon pustej aplikacji logiki. 
    > [!div class="mx-imgBorder"]
    > ![Pusty szablon](media/how-to-trigger-published-pipeline/blank-template.png)

1. W projektancie Wyszukaj **obiekt BLOB**. Wybierz opcję **gdy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)** , a następnie Dodaj ten wyzwalacz do aplikacji logiki.
    > [!div class="mx-imgBorder"]
    > ![Dodawanie wyzwalacza](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Wprowadź informacje o połączeniu dla konta usługi BLOB Storage, które chcesz monitorować pod kątem dodatków lub modyfikacji obiektów BLOB. Wybierz kontener do monitorowania. 
 
    Wybierz **Interwał** i **częstotliwość** sondowania w poszukiwaniu aktualizacji, które są dla Ciebie wykonywane.  

    > [!NOTE]
    > Ten wyzwalacz będzie monitorować wybrany kontener, ale nie będzie monitorował podfolderów.

1. Dodaj akcję HTTP, która będzie uruchamiana, gdy zostanie wykryty nowy lub zmodyfikowany obiekt BLOB. Wybierz pozycję **+ nowy krok**, a następnie wyszukaj i wybierz akcję http.

  > [!div class="mx-imgBorder"]
  > ![Wyszukaj akcję HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Aby skonfigurować akcję, użyj następujących ustawień:

  | Ustawienie | Wartość | 
  |---|---|
  | Akcja HTTP | POST |
  | URI |punkt końcowy do opublikowanego potoku, który został znaleziony jako [warunek wstępny](#prerequisites) |
  | Tryb uwierzytelniania | Tożsamość zarządzana |

1. Skonfiguruj harmonogram, aby ustawić wartość dowolnej [ścieżki dataPipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) :

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Użyj `DataStoreName` dodanych do obszaru roboczego jako [warunek wstępny](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Ustawienia protokołu HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Wybierz pozycję **Zapisz** , a harmonogram jest teraz gotowy.

> [!IMPORTANT]
> Jeśli używasz kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby zarządzania dostępem do potoku, [Ustaw uprawnienia dla scenariusza potoku (szkolenie lub ocenianie)](how-to-assign-roles.md#common-scenarios).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

> [!div class="nextstepaction"]
> [Korzystanie z potoków Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md)

* Dowiedz się więcej o [potokach](concept-ml-pipelines.md)
* Dowiedz się więcej o [eksplorowaniu Azure Machine Learning za pomocą Jupyter](samples-notebooks.md)

