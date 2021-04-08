---
title: Migracja z narzędzi do szacowania do klasy ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Przewodnik migracji z szacowania do ScriptRunConfig w celu konfigurowania zadań szkoleniowych.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518876"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrowanie z szacowania do ScriptRunConfig

Do tej pory istnieje wiele metod konfigurowania zadania szkoleniowego w Azure Machine Learning za pośrednictwem zestawu SDK, w tym szacowania, ScriptRunConfig i niższego poziomu RunConfiguration.   Aby rozwiązać tę niejednoznaczność i niespójność, upraszczamy proces konfiguracji zadania w usłudze Azure ML.  Teraz należy używać ScriptRunConfig jako zalecanej opcji konfigurowania zadań szkoleniowych. 

Szacowania są przestarzałe w wersji 1.19.0 zestawu Python SDK. Należy również często unikać bezpośredniego tworzenia wystąpienia obiektu RunConfiguration, a zamiast tego skonfigurować zadanie przy użyciu klasy ScriptRunConfig.

W tym artykule opisano typowe zagadnienia dotyczące migracji z programu szacowania do programu ScriptRunConfig.

> [!IMPORTANT]
> Aby przeprowadzić migrację do ScriptRunConfig z szacowania, upewnij się, że używasz >= 1.15.0 zestawu Python SDK.

## <a name="scriptrunconfig-documentation-and-samples"></a>Dokumentacja i przykłady ScriptRunConfig
Azure Machine Learning dokumentacja i przykłady zostały zaktualizowane w celu korzystania z [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) w celu konfigurowania i przekazywania zadań.

Aby uzyskać informacje na temat korzystania z programu ScriptRunConfig, zapoznaj się z następującą dokumentacją:
* [Konfigurowanie i przesyłanie przebiegów trenowania](how-to-set-up-training-targets.md)
* [Konfigurowanie przebiegów szkoleniowych PyTorch](how-to-train-pytorch.md)
* [Konfigurowanie przebiegów szkoleniowych TensorFlow](how-to-train-tensorflow.md)
* [Konfigurowanie scikit — nauka przebiegów szkoleniowych](how-to-train-scikit-learn.md)

Ponadto zapoznaj się z poniższymi przykładami & samouczków:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/uczenie — przykłady](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Definiowanie środowiska szkoleniowego
Chociaż różne struktury szacowania mają wstępnie skonfigurowane środowiska, które są obsługiwane przez obrazy platformy Docker, wieloetapowe dockerfile dla tych obrazów są prywatne.  W związku z tym nie masz dużej przejrzystości w zakresie tego, co zawierają te środowiska. Ponadto szacowania w odniesieniu do poszczególnych konstruktorów przyjmuje konfiguracje związane ze środowiskiem jako pojedyncze parametry (na przykład `pip_packages` , `custom_docker_image` ).

W przypadku korzystania z ScriptRunConfig wszystkie konfiguracje związane ze środowiskiem są hermetyzowane w `Environment` obiekcie, który jest przesyłany do `environment` parametru konstruktora ScriptRunConfig. Aby skonfigurować zadanie szkoleniowe, podaj środowisko, które ma wszystkie zależności wymagane dla skryptu szkoleniowego. Jeśli środowisko nie zostanie dostarczone, usługa Azure ML będzie używać jednego z obrazów podstawowych systemu Azure ML, a w przypadku `azureml.core.environment.DEFAULT_CPU_IMAGE` środowiska domyślnego. Istnieje kilka sposobów zapewnienia środowiska:

* [Korzystaj z nadzorowanych](how-to-use-environments.md#use-a-curated-environment) środowisk nadzorowanych w środowisku, które są domyślnie dostępne w obszarze roboczym. Istnieje odpowiednie środowisko nadzorowane dla każdego ze wstępnie skonfigurowanych obrazów platformy Docker Framework/Version, które są tworzone przez każdą strukturę szacowania.
* [Definiowanie własnego środowiska niestandardowego](how-to-use-environments.md)

Oto przykład użycia nadzorowanego środowiska PyTorch 1,6 na potrzeby szkoleń:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Jeśli chcesz określić **zmienne środowiskowe** , które zostaną ustawione w procesie, w którym wykonywany jest skrypt szkoleniowy, użyj obiektu Environment:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Aby uzyskać informacje na temat konfigurowania środowisk Azure ML i zarządzania nimi, zobacz:
* [Jak używać środowisk](how-to-use-environments.md)
* [Wyselekcjonowane środowiska](resource-curated-environments.md)
* [Uczenie się przy użyciu niestandardowego obrazu platformy Docker](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Używanie danych do szkolenia
### <a name="datasets"></a>Zestawy danych
Jeśli używasz zestawu danych usługi Azure ML do szkolenia, Przekaż zestaw danych jako argument do skryptu za pomocą `arguments` parametru. W ten sposób otrzymasz ścieżkę danych (punkt instalacji lub ścieżka pobierania) w skrypcie szkoleniowym za pośrednictwem argumentów.

Poniższy przykład służy do konfigurowania zadania szkoleniowego, w którym FileDataset, `mnist_ds` program zostanie zainstalowany na zdalnym obliczeniu.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (stary)
Chociaż zalecamy korzystanie z zestawów danych usługi Azure ML w ramach starego sposobu odwoływania się do DataReference, jeśli nadal używasz z jakichkolwiek przyczyn, należy skonfigurować zadanie w następujący sposób:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Aby uzyskać więcej informacji na temat korzystania z danych na potrzeby szkoleń, zobacz:
* [Uczenie się z zestawami danych w usłudze Azure ML](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Trenowanie rozproszone
Jeśli musisz skonfigurować zadanie rozproszone na potrzeby szkoleń, zrób to przez określenie `distributed_job_config` parametru w konstruktorze ScriptRunConfig. Przekaż [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)lub [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) dla zadań rozproszonych o odpowiednich typach.

Poniższy przykład służy do konfigurowania zadania szkoleniowego PyTorch do korzystania z szkolenia rozproszonego z MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Aby uzyskać więcej informacji, zobacz:
* [Trenowanie rozproszone przy użyciu platformy PyTorch](how-to-train-pytorch.md#distributed-training)
* [Uczenie rozproszone z TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Różne
Jeśli musisz uzyskać dostęp do bazowego obiektu RunConfiguration z dowolnego powodu, możesz to zrobić w następujący sposób:
```
src.run_config
```

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie i przesyłanie przebiegów trenowania](how-to-set-up-training-targets.md)