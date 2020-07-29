---
title: Uczenie modeli ML z szacowania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wykonywać szkolenia Jednowęzłowe i rozproszone dla tradycyjnych modeli uczenia maszynowego i uczenia głębokiego przy użyciu Azure Machine Learning klasie szacowania
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9f63b4215e9b4a67a439e47501876d237a6d3c3b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320922"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Uczenie modeli za pomocą Azure Machine Learning przy użyciu szacowania
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Za pomocą Azure Machine Learning można łatwo przesłać skrypt szkoleniowy do [różnych obiektów docelowych obliczeń](how-to-set-up-training-targets.md#compute-targets-for-training)przy użyciu [obiektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) i [obiektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Ten wzorzec zapewnia dużą elastyczność i maksymalną kontrolę.


Klasa szacowania ułatwia uczenie modeli o głębokiej uczeniu i wzmacnianiu uczenia się. Zapewnia abstrakcję wysokiego poziomu, która umożliwia łatwe konstruowanie konfiguracji uruchomieniowej. Można utworzyć i użyć generycznej [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptu szkoleniowego za pomocą wybranej platformy szkoleniowej (na przykład scikit-Learning) na dowolnym wybranym miejscu docelowym obliczeń, niezależnie od tego, czy jest to maszyna lokalna, jedna maszyna wirtualna na platformie Azure czy klaster GPU na platformie Azure. W przypadku zadań związanych z PyTorch, TensorFlow, łańcuchem i wzmocnieniem, Azure Machine Learning oferuje także odpowiednie [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [łańcucher](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)i [wzmocnienie kształcenia](how-to-use-reinforcement-learning.md) szacowania, aby uprościć korzystanie z tych platform.

## <a name="train-with-an-estimator"></a>Uczenie z szacowania

Po utworzeniu [obszaru roboczego](concept-workspace.md) i skonfigurowaniu [środowiska programistycznego](how-to-configure-environment.md)uczenie modelu w Azure Machine Learning obejmuje następujące kroki:  
1. Tworzenie [zdalnego obiektu docelowego obliczeń](how-to-set-up-training-targets.md) (Uwaga: Możesz również użyć komputera lokalnego jako obiektu docelowego obliczeń)
2. Przekaż [dane szkoleniowe](how-to-access-data.md) do magazynu danych (opcjonalnie)
3. Tworzenie [skryptu szkoleniowego](tutorial-train-models-with-aml.md#create-a-training-script)
4. Utworzenie obiektu `Estimator`
5. Prześlij szacowania do obiektu eksperymentu w obszarze roboczym

Ten artykuł koncentruje się na krokach 4-5. Kroki 1-3 można znaleźć na przykład w [samouczku uczenie modelu](tutorial-train-models-with-aml.md) .

### <a name="single-node-training"></a>Szkolenie z jednego węzła

W `Estimator` przypadku szkolenia z jednym węzłem należy użyć na potrzeby zdalnego obliczania na platformie Azure dla modelu scikit-uczenia się. Obiekt [docelowy obliczeń](how-to-set-up-training-targets.md#amlcompute) powinien już być utworzony `compute_target` i obiekt [FileDataset](how-to-create-register-datasets.md) `ds` .

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

Ten fragment kodu określa następujące parametry `Estimator` konstruktora.

Parametr | Opis
--|--
`source_directory`| Katalog lokalny, który zawiera cały kod wymagany do zadania szkoleniowego. Ten folder zostanie skopiowany z komputera lokalnego do zdalnego obliczenia.
`script_params`| Słownik określający argumenty wiersza polecenia do przekazania do skryptu szkoleniowego `entry_script` , w postaci `<command-line argument, value>` par. Aby określić flagę verbose w `script_params` , użyj `<command-line argument, "">` .
`compute_target`| Miejsce docelowe obliczeń zdalnych, na których będzie wykonywany skrypt szkoleniowy, w tym przypadku klaster Azure Machine Learning COMPUTE ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Należy pamiętać, że klaster AmlCompute jest często używanym miejscem docelowym, dlatego można wybrać inne typy obiektów docelowych obliczeń, takie jak maszyny wirtualne platformy Azure, a nawet komputer lokalny).
`entry_script`| FilePath (względem `source_directory` ) skryptu szkoleniowego do uruchomienia w ramach obliczeń zdalnych. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny znajdować się w tym folderze.
`conda_packages`| Lista pakietów języka Python do zainstalowania za pośrednictwem Conda wymaganego przez skrypt szkoleniowy.  

Konstruktor ma inny parametr o nazwie `pip_packages` , który jest używany dla wszystkich wymaganych pakietów PIP.

Teraz, po utworzeniu `Estimator` obiektu, Prześlij zadanie szkolenia, które ma zostać uruchomione na zdalnym obliczeniu, za pomocą wywołania `submit` funkcji w obiekcie [eksperymentu](concept-azure-machine-learning-architecture.md#experiments) `experiment` . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Foldery specjalne** Dwa foldery, dane *wyjściowe* i *dzienniki*, otrzymują specjalne traktowanie według Azure Machine Learning. Podczas szkolenia podczas pisania plików do folderów o nazwie dane *wyjściowe* i *dzienniki* , które są względne dla katalogu głównego ( `./outputs` i `./logs` odpowiednio), pliki zostaną automatycznie przekazane do historii uruchamiania, dzięki czemu będziesz mieć do nich dostęp po zakończeniu przebiegu.
>
> Aby tworzyć artefakty podczas szkoleń (takich jak pliki modelu, punkty kontrolne, pliki danych lub rysunki), Zapisz je w `./outputs` folderze.
>
> Podobnie można napisać wszystkie dzienniki z poziomu szkolenia szkoleniowego do `./logs` folderu. Aby korzystać z [integracji TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) Azure Machine Learning upewnij się, że piszesz dzienniki TensorBoard w tym folderze. Gdy przebieg jest w toku, będzie można uruchamiać TensorBoard i przesyłać strumieniowo te dzienniki.  Później będzie można przywrócić dzienniki z dowolnego poprzedniego przebiegu.
>
> Na przykład, aby pobrać plik zapisany *w folderze* Outputs na komputer lokalny po uruchomieniu szkolenia zdalnego:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Szkolenie rozproszone i niestandardowe obrazy platformy Docker

Istnieją dwa dodatkowe scenariusze szkoleniowe, które można wykonać przy użyciu `Estimator` :
* Używanie niestandardowego obrazu platformy Docker
* Szkolenie rozproszone w klastrze z obsługą kilku węzłów

Poniższy kod przedstawia sposób przeprowadzenia szkolenia rozproszonego dla modelu Keras. Ponadto zamiast używać domyślnych obrazów Azure Machine Learning, określa niestandardowy obraz platformy Docker z usługi Docker Hub `continuumio/miniconda` do szkoleń.

Obiekt [docelowy obliczeń](how-to-set-up-training-targets.md#amlcompute) powinien już być utworzony `compute_target` . Szacowania można utworzyć w następujący sposób:

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

Powyższy kod uwidacznia następujące nowe parametry `Estimator` konstruktorowi:

Parametr | Opis | Domyślne
--|--|--
`custom_docker_image`| Nazwa obrazu, którego chcesz użyć. Udostępniaj tylko obrazy dostępne w publicznych repozytoriach platformy Docker (w tym przypadku Docker Hub). Aby użyć obrazu z prywatnego repozytorium platformy Docker, użyj `environment_definition` zamiast niego parametru konstruktora. [Zobacz przykład](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Liczba węzłów, które mają być używane dla zadania szkoleniowego. | `1`
`process_count_per_node`| Liczba procesów (lub "pracowników") do uruchomienia w każdym węźle. W takim przypadku używane są `2` procesory GPU dostępne w każdym węźle.| `1`
`distributed_training`| Obiekt [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) do uruchamiania szkoleń rozproszonych przy użyciu zaplecza MPI.  | `None`


Na koniec Prześlij zadanie szkoleniowe:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Rejestrowanie modelu

Po przeszkoleniu modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

Uruchomienie następującego kodu spowoduje zarejestrowanie modelu w Twoim obszarze roboczym i udostępnienie go do odwołania według nazwy w zdalnych kontekstach obliczeniowych lub w skryptach wdrożenia. [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)Więcej informacji i dodatkowe parametry można znaleźć w dokumentacji referencyjnej.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Śledzenie i integracja z usługą GitHub

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Przykłady
Aby zapoznać się z notesem, który pokazuje podstawy wzorca szacowania, zobacz:
* [Jak korzystać z platformy Azure/uczenie się — uczenie się — nauka/poznanie — szacowania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

W przypadku notesu, który pociąga za scikit model uczenia przy użyciu szacowania, zobacz:
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

W przypadku notesów na potrzeby modeli szkoleniowych przy użyciu szacowania z konkretną nauką, zobacz:

* [Jak używać — platforma Azure/ml — platformy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)
* [Szkolenie modeli PyTorch](how-to-train-pytorch.md)
* [Szkolenie modeli TensorFlow](how-to-train-tensorflow.md)
* [Uczenie głębokiej sieci neuronowych Learning uczenia się](how-to-use-reinforcement-learning.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Twórz środowiska szkoleniowe i wdrażaj i zarządzaj nimi](how-to-use-environments.md)