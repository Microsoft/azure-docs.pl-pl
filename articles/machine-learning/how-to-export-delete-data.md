---
title: Eksportowanie lub usuwanie danych obszaru roboczego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyeksportować lub usunąć obszar roboczy za pomocą interfejsów API platformy Azure Machine Learning Studio, interfejsu wiersza polecenia, zestawu SDK i uwierzytelniane.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c4f48acc2d6e57dea0a8db2a149d7ca2871c9f39
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072007"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Eksportowanie lub usuwanie danych obszaru roboczego usługi Machine Learning

W Azure Machine Learning można eksportować lub usuwać dane obszaru roboczego za pomocą interfejsu graficznego portalu lub zestawu SDK języka Python. W tym artykule opisano obie opcje.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrolowanie danych obszaru roboczego

Dane w produkcie przechowywane przez Azure Machine Learning są dostępne do eksportowania i usuwania. Możesz eksportować i usuwać za pomocą Azure Machine Learning Studio, CLI i SDK. Dostęp do danych telemetrycznych można uzyskać za pomocą portalu ochrony prywatności systemu Azure. 

W Azure Machine Learning dane osobowe składają się z informacji o użytkowniku w dokumentach historii uruchamiania. 

## <a name="delete-high-level-resources-using-the-portal"></a>Usuwanie zasobów wysokiego poziomu przy użyciu portalu

Podczas tworzenia obszaru roboczego platforma Azure tworzy wiele zasobów w grupie zasobów:

- Sam obszar roboczy
- Konto magazynu
- Rejestr kontenerów
- Wystąpienie usługi Application Insights
- Magazyn kluczy

Te zasoby można usunąć, wybierając je z listy i wybierając pozycję **Usuń** . 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Zrzut ekranu portalu z wyróżnioną ikoną usuwania":::

Uruchom dokumenty historii, które mogą zawierać osobiste informacje o użytkowniku, są przechowywane na koncie magazynu w magazynie obiektów BLOB w podfolderach `/azureml` . Dane można pobrać i usunąć z portalu.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Zrzut ekranu przedstawiający katalog usługi Azure Storage na koncie magazynu w portalu":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Eksportowanie i usuwanie zasobów uczenia maszynowego za pomocą programu Azure Machine Learning Studio

Azure Machine Learning Studio zapewnia ujednolicony widok zasobów uczenia maszynowego, takich jak notesy, zestawy danych, modele i eksperymenty. Azure Machine Learning Studio podkreśla zachowywanie rekordu danych i eksperymentów. Zasoby obliczeniowe, takie jak potoki i zasoby obliczeniowe, można usunąć za pomocą przeglądarki. W przypadku tych zasobów przejdź do podanego zasobu, a następnie wybierz pozycję **Usuń**. 

Zestawy danych mogą być wyrejestrowane, a eksperymenty mogą być archiwizowane, ale te operacje nie usuwają tych dane. Aby całkowicie usunąć dane, zestawy danych i dane uruchomieniowe muszą zostać usunięte na poziomie magazynu. Usuwanie na poziomie magazynu odbywa się przy użyciu portalu, zgodnie z wcześniejszym opisem.

Artefakty szkoleniowe można pobrać z doświadczalnych przebiegów przy użyciu programu Studio. Wybierz **eksperyment** i **Uruchom** , w którym Cię interesujesz. Wybierz pozycję **dane wyjściowe + dzienniki** i przejdź do określonych artefaktów, które chcesz pobrać. Wybierz pozycję **...** i **Pobierz**.

Możesz pobrać zarejestrowany model, przechodząc do odpowiedniego **modelu** i wybierając pozycję **Pobierz**. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Zrzut ekranu strony modelu Studio z wyróżnioną opcją pobrania":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Eksportowanie i usuwanie zasobów przy użyciu zestawu SDK języka Python

Dane wyjściowe określonego uruchomienia można pobrać przy użyciu polecenia: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Poniższe zasoby uczenia maszynowego można usunąć za pomocą zestawu SDK języka Python: 

| Typ | Wywołanie funkcji | Uwagi | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | Użyj `delete-dependent-resources` do kaskadowego usunięcia |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py) | |