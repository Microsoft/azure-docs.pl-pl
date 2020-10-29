---
title: Wdrażanie modeli szkolonych w projektancie przy użyciu programu Studio
titleSuffix: Azure Machine Learning
description: Użyj Azure Machine Learning Studio do wdrażania modeli szkolonych w projektancie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio
ms.openlocfilehash: 0d98d5103e26eb0b4ee0d31b95f1d07cdaa396ae
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927587"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Wdrażanie modeli szkolonych w projektancie przy użyciu programu Studio

W tym artykule dowiesz się, jak wdrożyć przeszkolony model z projektanta jako punkt końcowy w czasie rzeczywistym w programie Azure Machine Learning Studio.

Wdrożenie w programie Studio obejmuje następujące kroki:

1. Zarejestruj przeszkolony model.
1. Pobierz skrypt wpisu i plik zależności Conda dla modelu.
1. Obowiązkowe Skonfiguruj skrypt wprowadzania.
1. Wdróż model w obiekcie docelowym obliczeń.

Modele można także wdrażać bezpośrednio w projektancie, aby pominąć rejestrację modelu i procedurę pobierania plików. Może to być przydatne do szybkiego wdrażania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modelu przy użyciu projektanta](tutorial-designer-automobile-price-deploy.md).

Modele przeszkolone w projektancie można także wdrażać za pomocą zestawu SDK lub interfejsu wiersza polecenia (CLI). Aby uzyskać więcej informacji, zobacz [wdrażanie istniejącego modelu przy użyciu Azure Machine Learning](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

* Ukończony potok szkoleniowy zawierający jeden z następujących modułów:
    - [Moduł uczenia modelu](./algorithm-module-reference/train-model.md)
    - [Moduł wykrywania anomalii szkolenia](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Moduł uczenie modelu klastra](./algorithm-module-reference/train-clustering-model.md)
    - [Moduł szkolenia Pytorch model](./algorithm-module-reference/train-pytorch-model.md)
    - [Moduł polecający SVD](./algorithm-module-reference/train-svd-recommender.md)
    - [Uczenie modułu Vowpal Wabbit model](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [& moduł głębokiego modelu uczenia](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Rejestrowanie modelu

Po zakończeniu potoku szkoleniowego Zarejestruj przeszkolony model w obszarze roboczym Azure Machine Learning, aby uzyskać dostęp do modelu w innych projektach.

1. Wybierz [moduł uczenie modelu](./algorithm-module-reference/train-model.md).
1. W okienku po prawej stronie wybierz kartę dane **wyjściowe + dzienniki** .
1. Wybierz ikonę **zarejestruj model** ![ zrzut ekranu ikony koła zębatego ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Zrzut ekranu przedstawiający prawy panel modułu uczenie modelu](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Wprowadź nazwę dla modelu, a następnie wybierz pozycję **Zapisz** .

Po zarejestrowaniu modelu można go znaleźć na stronie zasobów **modeli** w Studio.
    
![Zrzut ekranu zarejestrowanego modelu na stronie zasobów modeli](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Pobierz plik skryptu wpisu i plik zależności Conda

Do wdrożenia modelu w programie Azure Machine Learning Studio potrzebne są następujące pliki:

- **Plik skryptu wpisu** — ładuje przeszkolony model, przetwarza dane wejściowe z żądań, wykonuje wnioski w czasie rzeczywistym i zwraca wynik. Projektant automatycznie generuje `score.py` plik skryptu wejścia po zakończeniu modułu **uczenia modelu** .

- **Plik zależności Conda** — określa, które pakiety PIP i Conda, od których zależy usługa sieci Web. Projektant automatycznie tworzy `conda_env.yaml` plik po zakończeniu modułu **uczenia modelu** .

Te dwa pliki można pobrać w prawym okienku modułu **uczenie modelu** :

1. Wybierz moduł **Train Model** (Trenowanie modelu).
1. Na karcie dane **wyjściowe i dzienniki** wybierz folder `trained_model_outputs` .
1. Pobierz `conda_env.yaml` plik i `score.py` plik.

    ![Zrzut ekranu przedstawiający pliki do wdrożenia w okienku po prawej stronie](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Alternatywnie można pobrać pliki ze strony zasobów **modeli** po zarejestrowaniu modelu:

1. Przejdź do strony **modele** zasobów.
1. Wybierz model, który chcesz wdrożyć.
1. Wybierz kartę **artefakty** .
1. Wybierz folder `trained_model_outputs`.
1. Pobierz `conda_env.yaml` plik i `score.py` plik.  

    ![Zrzut ekranu przedstawiający pliki do wdrożenia na stronie szczegółów modelu](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> `score.py`Plik zapewnia niemal te same funkcje co moduły **modelu oceny** . Jednak niektóre moduły, takie jak [zalecana Ocena SVD](./algorithm-module-reference/score-svd-recommender.md), [Ocena o szerokim i głębokim](./algorithm-module-reference/score-wide-and-deep-recommender.md)wykorzystaniu, oraz [Vowpaly model Wabbit](./algorithm-module-reference/score-vowpal-wabbit-model.md) mają parametry dla różnych trybów oceniania. Możesz również zmienić te parametry w skrypcie wprowadzania.
>
>Aby uzyskać więcej informacji na temat ustawiania parametrów w `score.py` pliku, zobacz sekcję [Konfigurowanie skryptu wejścia](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Po pobraniu niezbędnych plików można przystąpić do wdrażania modelu.

1. Na stronie zasoby **modeli** Wybierz zarejestrowany model.
1. Wybierz przycisk **Wdróż** .
1. W menu Konfiguracja wprowadź następujące informacje:

    - Wprowadź nazwę dla punktu końcowego.
    - Wybierz wdrożenie modelu w [usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) lub [wystąpieniu kontenera platformy Azure](how-to-deploy-azure-container-instance.md).
    - Przekaż `score.py` **plik skryptu do wpisu** .
    - Przekaż `conda_env.yml` **plik dla zależności Conda** . 

    >[!TIP]
    > W ustawieniu **zaawansowanym** można ustawić pojemność procesora/pamięci oraz inne parametry wdrożenia. Te ustawienia są ważne w przypadku niektórych modeli, takich jak modele PyTorch, które zużywają znaczną ilość Memery (około 4 GB).

1. Wybierz pozycję **Wdróż** , aby wdrożyć model jako punkt końcowy w czasie rzeczywistym.

    ![Zrzut ekranu przedstawiający model wdrażania na stronie zasobów modelu](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Korzystanie z punktu końcowego w czasie rzeczywistym

Po pomyślnym wdrożeniu można znaleźć punkt końcowy w czasie rzeczywistym na stronie zasobów **punktów końcowych** . W tym miejscu znajduje się punkt końcowy REST, którego klienci mogą używać do przesyłania żądań do punktu końcowego w czasie rzeczywistym. 

> [!NOTE]
> Projektant generuje również przykładowy plik JSON danych do testowania, można go pobrać `_samples.json` w folderze **trained_model_outputs** .

Użyj poniższego przykładu kodu, aby korzystać z punktu końcowego w czasie rzeczywistym.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Korzystanie z punktów końcowych związanych z obsługą komputerów w czasie rzeczywistym

W przypadku korzystania z punktów końcowych związanych z obsługą komputera w czasie rzeczywistym należy skonwertować obrazy na bajty, ponieważ usługa sieci Web akceptuje tylko ciąg jako dane wejściowe. Poniżej znajduje się przykładowy kod:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Konfigurowanie skryptu wprowadzania

W przypadku niektórych modułów w projektancie, takich jak [zalecenia dotyczące oceny SVD](./algorithm-module-reference/score-svd-recommender.md), [szerokiej i szczegółowej](./algorithm-module-reference/score-wide-and-deep-recommender.md)oceny i [oceny Vowpal Wabbit](./algorithm-module-reference/score-vowpal-wabbit-model.md) , mają parametry dla różnych trybów oceniania. 

W tej sekcji dowiesz się, jak zaktualizować te parametry w pliku skryptu wejścia.

Poniższy przykład aktualizuje zachowanie domyślne dla przeszkolonego **& głębokiego modelu zalecanego** . Domyślnie `score.py` plik instruuje usługę sieci Web do przewidywania klasyfikacji między użytkownikami i elementami. 

Można zmodyfikować plik skryptu wpisu, aby wykonać zalecenia dotyczące elementów i zwrócić zalecane elementy przez zmianę `recommender_prediction_kind` parametru.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Aby uzyskać **szerokie & głębokiego polecania** i modeli **Wabbit Vowpal** , można skonfigurować parametr trybu oceniania przy użyciu następujących metod:

- Nazwy parametrów to kombinacje małych i podkreśleń nazw parametrów dla [oceny Vowpal Wabbit](./algorithm-module-reference/score-vowpal-wabbit-model.md) i [szerokiego i szczegółowego zalecenia](./algorithm-module-reference/score-wide-and-deep-recommender.md);
- Wartości parametrów typu Mode są ciągami odpowiednich nazw opcji. W powyższych kodach należy wykonać **proponowany rodzaj przewidywania** , ale może to być wartość `'Rating Prediction'` lub `'Item Recommendation'` . Inne wartości są niedozwolone.

W przypadku modelu szkolenia z **polecanymi** metodami SVD nazwy i wartości parametrów mogą być mniej oczywiste i można wyszukać tabele poniżej, aby określić sposób ustawiania parametrów.

| Nazwa parametru w [wyliczonym lecie](./algorithm-module-reference/score-svd-recommender.md)                           | Nazwa parametru w pliku skryptu wpisu |
| ------------------------------------------------------------ | --------------------------------------- |
| Rodzaj przewidywania polecania                                  | prediction_kind                         |
| Wybór zalecanego elementu                                   | recommended_item_selection              |
| Minimalny rozmiar puli rekomendacji dla pojedynczego użytkownika    | min_recommendation_pool_size            |
| Maksymalna liczba elementów, które mają być zalecane dla użytkownika               | max_recommended_item_count              |
| Czy zwrócić przewidywane klasyfikacje elementów wraz z etykietami | return_ratings                          |

Poniższy kod pokazuje, jak ustawić parametry dla zalecanego zalecenia, które używa wszystkich sześciu parametrów, aby zalecać sklasyfikowane elementy z dołączoną klasyfikacją.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu w projektancie](tutorial-designer-automobile-price-train-score.md)
* [Wdrażanie modeli przy użyciu zestawu SDK Azure Machine Learning](how-to-deploy-and-where.md)
* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
