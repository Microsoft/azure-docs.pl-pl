---
title: Trenuj modele ML z estymatorami
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przeprowadzać szkolenia jednowęzłowe i rozproszone dotyczące tradycyjnych modeli uczenia maszynowego i uczenia głębokiego przy użyciu klasy Azure Machine Learning Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: a9a3c10687b92d946fccb282f2eda4af637cf2ab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686768"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Trenuj modele za pomocą usługi Azure Machine Learning przy użyciu estymatora
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Za pomocą usługi Azure Machine Learning można łatwo przesłać skrypt szkoleniowy do [różnych celów obliczeniowych,](how-to-set-up-training-targets.md#compute-targets-for-training)używając [obiektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) i [obiektu ScriptRunConfig.](how-to-set-up-training-targets.md#submit) Ten wzór zapewnia dużą elastyczność i maksymalną kontrolę.

Aby ułatwić szkolenie modelu uczenia głębokiego, zestaw SDK języka Python usługi Azure Machine Learning zawiera alternatywną abstrakcję wyższego poziomu, klasę estymatora, która umożliwia użytkownikom łatwe konstruowanie konfiguracji uruchamiania. Można utworzyć i używać ogólnego [estymatora](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptu szkoleniowego przy użyciu dowolnej platformy szkoleniowej, którą wybierzesz (na przykład scikit-learn) na dowolnym miejscu docelowym obliczeń, który wybierzesz, niezależnie od tego, czy jest to komputer lokalny, pojedyncza maszyna wirtualna na platformie Azure, czy klaster gpu na platformie Azure. W przypadku zadań PyTorch, TensorFlow i Chainer usługa Azure Machine Learning zapewnia również odpowiednie estymatory [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) aby uprościć korzystanie z tych struktur.

## <a name="train-with-an-estimator"></a>Pociąg z estymatorem

Po utworzeniu [obszaru roboczego](concept-workspace.md) i skonfigurowaniu [środowiska programistycznego](how-to-configure-environment.md)szkolenie modelu w usłudze Azure Machine Learning obejmuje następujące kroki:  
1. Tworzenie [zdalnego celu obliczeniowego](how-to-set-up-training-targets.md) (uwaga, że można również użyć komputera lokalnego jako obiektu docelowego obliczeń)
2. Przekazywanie [danych szkoleniowych](how-to-access-data.md) do magazynu danych (opcjonalnie)
3. Tworzenie [skryptu szkoleniowego](tutorial-train-models-with-aml.md#create-a-training-script)
4. Utworzenie obiektu `Estimator`
5. Przesyłanie estymatora do obiektu eksperymentu pod obszarem roboczym

W tym artykule skupiono się na krokach 4-5. W przypadku kroków 1-3 zapoznaj się z [instrukcją samouczek modelu](tutorial-train-models-with-aml.md) na przykład.

### <a name="single-node-training"></a>Szkolenie z jednego węzła

Użyj `Estimator` dla jednego węzła szkolenia uruchomić na zdalnych obliczeń na platformie Azure dla modelu scikit-learn. Obiekt [docelowy obliczeń](how-to-set-up-training-targets.md#amlcompute) i `compute_target` obiekt `ds` [FileDataset](how-to-create-register-datasets.md) powinien być już utworzony.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Ten fragment kodu określa następujące parametry do `Estimator` konstruktora.

Parametr | Opis
--|--
`source_directory`| Katalog lokalny, który zawiera cały kod potrzebny do zadania szkoleniowego. Ten folder zostanie skopiowany z komputera lokalnego do zdalnego obliczeń.
`script_params`| Słownik określający argumenty wiersza polecenia, które `entry_script`mają być przerzucene do skryptu szkoleniowego, `<command-line argument, value>` w postaci par. Aby określić szczegółową flagę `script_params` `<command-line argument, "">`w pliku , użyj pliku .
`compute_target`| Zdalny obiekt docelowy obliczeń, na który będzie uruchamiany skrypt szkoleniowy, w tym przypadku klaster obliczeń usługi Azure Machine Learning Compute[(AmlCompute).](how-to-set-up-training-targets.md#amlcompute) (Należy zauważyć, mimo że klaster AmlCompute jest powszechnie używanym obiektem docelowym, można również wybrać inne typy obiektów docelowych obliczeniowych, takie jak maszyny wirtualne platformy Azure lub nawet komputer lokalny).
`entry_script`| Ścieżka pliku `source_directory`(względem) skryptu szkoleniowego, który ma być uruchamiany na komputerze zdalnym. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny znajdować się w tym folderze.
`conda_packages`| Lista pakietów Pythona do zainstalowania za pośrednictwem conda potrzebne przez skrypt szkolenia.  

Konstruktor ma inny `pip_packages` parametr o nazwie, który jest używany dla wszystkich pakietów pip potrzebne.

Po `Estimator` utworzeniu obiektu należy przesłać zadanie szkoleniowe do uruchomienia na komputerze zdalnym za pomocą `experiment`wywołania `submit` funkcji obiektu [Eksperyment](concept-azure-machine-learning-architecture.md#experiments) . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Foldery specjalne** Dwa foldery, *dane wyjściowe* i *dzienniki*, otrzymują specjalne traktowanie przez usługę Azure Machine Learning. Podczas szkolenia podczas pisania plików do folderów o nazwie *wyjścia* i *dzienniki,* które są związane z katalogiem głównym (i`./outputs` `./logs`, odpowiednio), pliki będą automatycznie przesyłane do historii uruchamiania, dzięki czemu masz do nich dostęp po zakończeniu biegu.
>
> Aby utworzyć artefakty podczas szkolenia (takie jak pliki modelu, punkty kontrolne, `./outputs` pliki danych lub drukowane obrazy) zapisz je w folderze.
>
> Podobnie można zapisać dowolne dzienniki z `./logs` przebiegu szkolenia do folderu. Aby korzystać z [integracji tensorboard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) usługi Azure Machine Learning, upewnij się, że piszesz dzienniki TensorBoard w tym folderze. Podczas biegu jest w toku, będzie można uruchomić TensorBoard i strumień tych dzienników.  Później będzie można również przywrócić dzienniki z dowolnego z poprzednich uruchomień.
>
> Na przykład, aby pobrać plik zapisany w folderze *wyjścia* na komputerze lokalnym po uruchomieniu szkolenia zdalnego:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Rozproszone szkolenia i niestandardowe obrazy platformy Docker

Istnieją dwa dodatkowe scenariusze szkolenia, które `Estimator`można przeprowadzić z:
* Używanie niestandardowego obrazu platformy Docker
* Szkolenie rozproszone w klastrze wielowęzłowym

Poniższy kod pokazuje, jak przeprowadzić szkolenia rozproszone dla modelu Keras. Ponadto zamiast używać domyślnych obrazów usługi Azure Machine Learning, określa niestandardowy obraz `continuumio/miniconda` docker z Usługi Docker Hub do szkolenia.

Obiekt [docelowy obliczeń](how-to-set-up-training-targets.md#amlcompute) powinien `compute_target`już zostać utworzony. Estymatora można utworzyć w następujący sposób:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Powyższy kod udostępnia następujące nowe `Estimator` parametry do konstruktora:

Parametr | Opis | Domyślne
--|--|--
`custom_docker_image`| Nazwa obrazu, którego chcesz użyć. Udostępniaj tylko obrazy dostępne w publicznych repozytoriach dokowane (w tym przypadku Centrum platformy Docker). Aby użyć obrazu z prywatnego repozytorium docker, należy `environment_definition` użyć parametru konstruktora. [Zobacz przykład](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Liczba węzłów do użycia w pracy szkoleniowej. | `1`
`process_count_per_node`| Liczba procesów (lub "pracowników") do uruchomienia w każdym węźle. W takim przypadku należy `2` użyć procesorów GPU dostępnych w każdym węźle.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) obiekt do uruchamiania szkolenia rozproszonego przy użyciu zaplecza MPI.  | `None`


Na koniec należy przedłożyć zadanie szkoleniowe:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Rejestrowanie modelu

Po przeszkoleniu modelu można go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i wdrażania.](concept-model-management-and-deployment.md)

Uruchomienie następującego kodu spowoduje zarejestrowanie modelu w obszarze roboczym i udostępnienie go do odwoływania się za pomocą nazwy w kontekstach obliczeniowych zdalnych lub skryptach wdrażania. Zobacz [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) w dokumentach referencyjnych, aby uzyskać więcej informacji i dodatkowe parametry.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Śledzenie i integracja GitHub

Po uruchomieniu przebiegu szkolenia, w którym katalog źródłowy jest lokalnym repozytorium Git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Integracja git dla usługi Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Przykłady
Aby uzyskać notes, który zawiera podstawy wzorca estymatora, zobacz:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estymator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Aby uzyskać notes, który trenuje model scikit-learn za pomocą estymatora, zobacz:
* [tutoriale/img-klasyfikacja-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

W przypadku notesów dotyczących modeli szkoleniowych przy użyciu estymatorów specyficznych dla struktury uczenia głębokiego zobacz:

* [struktury how-to-use-azureml/ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Śledzenie danych o przebiegu podczas treningu](how-to-track-experiments.md)
* [Szkolenie modeli PyTorch](how-to-train-pytorch.md)
* [Szkolenie modeli TensorFlow](how-to-train-tensorflow.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi](how-to-use-environments.md)