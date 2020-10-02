---
title: Użycie pamięci modelu profilowego i procesora CPU
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak profilować pamięć modelu i użycie procesora CPU
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: aac5fba68c43892216cbd16dd99b0c6a9bf70217
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660993"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Profilowanie modelu w celu określenia wykorzystania zasobów

W tym artykule pokazano, jak profilować Uczenie maszynowe w modelu, aby określić, ile procesora i pamięci należy przydzielić dla modelu podczas wdrażania go jako usługi sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz przeszkolony i zarejestrowany model z Azure Machine Learning. Zobacz [przykładowy samouczek](how-to-train-scikit-learn.md) , aby zapoznać się z przykładem szkoleń i rejestrowania modelu scikit-uczyć przy użyciu Azure Machine Learning.

## <a name="limitations"></a>Ograniczenia

* Profilowanie nie będzie działało, gdy Azure Container Registry (ACR) dla Twojego obszaru roboczego znajduje się za siecią wirtualną.

## <a name="run-the-profiler"></a>Uruchamianie profilera

Po zarejestrowaniu modelu i przygotowaniu innych składników niezbędnych do wdrożenia można określić procesor i pamięć, która będzie potrzebna przez wdrożoną usługę. Profilowanie testuje usługę, która uruchamia model i zwraca informacje takie jak użycie procesora CPU, użycie pamięci i opóźnienie odpowiedzi. Zawiera również zalecenia dotyczące procesora CPU i pamięci na podstawie użycia zasobów.

Aby profilować model, potrzebne są:
* Zarejestrowany model.
* Konfiguracja wnioskowania na podstawie skryptu wejścia i definicji środowiska wnioskowania.
* Pojedynczy kolumnowy tabelaryczny zestaw danych, gdzie każdy wiersz zawiera ciąg reprezentujący przykładowe dane żądania.

> [!IMPORTANT]
> W tym momencie obsługujemy tylko profilowanie usług, które oczekują, aby dane żądania były ciągiem, na przykład: szeregowany ciąg JSON, tekst, serializowany ciąg znaków itd. Zawartość każdego wiersza zestawu danych (String) zostanie umieszczona w treści żądania HTTP i wysłana do usługi hermetyzowania modelu na potrzeby oceniania.

> [!IMPORTANT]
> Obsługujemy profilowanie maksymalnie 2 procesorów CPU w regionie ChinaEast2 i USGovArizona.

Poniżej znajduje się przykład sposobu konstruowania wejściowego zestawu danych w celu profilowania usługi, która oczekuje, że jej przychodzące dane żądania zawierają Zserializowany kod JSON. W takim przypadku utworzyliśmy zestaw danych oparty na 100 wystąpieniach tej samej zawartości danych żądania. W realnie świecie zalecamy korzystanie z większych zestawów danych zawierających różne dane wejściowe, zwłaszcza jeśli użycie/zachowanie zasobów modelu jest zależne od danych wejściowych.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Gdy zestaw danych zawierający przykładowe dane żądania są gotowe, Utwórz konfigurację wnioskowania. Konfiguracja wnioskowania jest oparta na score.py i definicji środowiska. W poniższym przykładzie pokazano, jak utworzyć konfigurację wnioskowania i uruchomić profilowanie:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


Następujące polecenie pokazuje, jak profilować model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Aby zachować informacje zwracane przez profilowanie, użyj tagów lub właściwości dla modelu. Użycie tagów lub właściwości przechowuje dane w modelu w rejestrze modelu. W poniższych przykładach pokazano, jak dodać nowy tag `requestedCpu` zawierający `requestedMemoryInGb` informacje i:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)

