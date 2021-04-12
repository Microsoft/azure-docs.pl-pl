---
title: Używanie Apache Spark w potoku uczenia maszynowego (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Połącz obszar roboczy usługi Azure Synapse Analytics z potokiem usługi Azure Machine Learning, aby użyć Apache Spark na potrzeby manipulowania danymi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: b03915608c6143a9e205ba1a1e08e411b8aa9093
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868651"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Jak używać Apache Spark (obsługiwane przez usługę Azure Synapse Analytics) w potoku uczenia maszynowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak używać pul Apache Spark obsługiwanych przez usługę Azure Synapse Analytics jako element docelowy obliczeń dla kroku przygotowywania danych w potoku Azure Machine Learning. Dowiesz się, w jaki sposób pojedynczy potok może korzystać z zasobów obliczeniowych, które są odpowiednie dla określonego kroku, takiego jak przygotowanie danych lub szkolenie. Zobaczysz, jak dane są przygotowywane do kroku Spark i jak jest on przenoszona do następnego kroku. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) , aby pomieścić wszystkie zasoby potoku.

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning](concept-compute-instance.md) z już zainstalowanym zestawem SDK.

* Utwórz obszar roboczy usługi Azure Synapse Analytics i pulę Apache Spark (zobacz [Szybki Start: Tworzenie puli Apache Spark bezserwerowej przy użyciu programu Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Połącz obszar roboczy Azure Machine Learning i obszar roboczy usługi Azure Synapse Analytics 

Możesz tworzyć pule Apache Spark i zarządzać nimi w obszarze roboczym usługi Azure Synapse Analytics. Aby zintegrować pulę Apache Spark z obszarem roboczym Azure Machine Learning, należy [połączyć się z obszarem roboczym usługi Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md). 

Pulę Apache Spark można dołączyć za pośrednictwem interfejsu użytkownika programu Azure Machine Learning Studio, korzystając ze strony **połączone usługi** . Można to zrobić również za pośrednictwem strony **obliczeniowej** z opcją **Attach COMPUTE** .

Możesz również dołączyć pulę Apache Spark za pośrednictwem zestawu SDK (jak pokazano poniżej) lub za pośrednictwem szablonu ARM (zobacz ten [przykładowy szablon ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

Można użyć wiersza polecenia, aby postępować zgodnie z szablonem ARM, dodać połączoną usługę i dołączyć pulę Apache Spark przy użyciu następującego kodu:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Aby pomyślnie połączyć się z obszarem roboczym usługi Azure Synapse Analytics, musisz mieć rolę właściciela w zasobie obszaru roboczego analizy usługi Azure Synapse. Sprawdź swój dostęp w Azure Portal.
> Połączona Usługa otrzyma tożsamość przypisaną przez system (SAI) podczas jej tworzenia. Należy przypisać tę usługę linku do roli "SAI Apache Spark administrator" z programu Synapse Studio, aby mogła ona przesłać zadanie usługi Spark (zobacz [jak zarządzać przypisaniami ról RBAC w programie Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Należy również przyznać użytkownikowi Azure Machine Learning obszar roboczy roli "Współautor" z Azure Portal zarządzania zasobami.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Utwórz lub Pobierz link między obszarem roboczym usługi Azure Synapse Analytics i obszarem roboczym Azure Machine Learning

Połączone usługi można pobrać w obszarze roboczym, korzystając z kodu takiego jak:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Najpierw `Workspace.from_config()` Uzyskuj dostęp do obszaru roboczego Azure Machine Learning przy użyciu konfiguracji programu `config.json` (zobacz [Samouczek: wprowadzenie do Azure Machine Learning w środowisku programistycznym](tutorial-1st-experiment-sdk-setup-local.md)). Następnie kod drukuje wszystkie połączone usługi dostępne w obszarze roboczym. Na koniec `LinkedService.get()` Pobiera połączoną usługę o nazwie `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Dołącz pulę platformy Apache Spark jako obiekt docelowy obliczeń dla Azure Machine Learning

Aby skorzystać z puli platformy Apache Spark w celu przełączenia kroku w potoku uczenia maszynowego, należy dołączyć go jako `ComputeTarget` dla kroku potoku, jak pokazano w poniższym kodzie.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

Pierwszym krokiem jest skonfigurowanie `SynapseCompute` . `linked_service`Argument jest `LinkedService` obiektem utworzonym lub pobranym w poprzednim kroku. `type`Argument musi być `SynapseSpark` . `pool_name`Argument w `SynapseCompute.attach_configuration()` musi być zgodny z zakresem istniejącej puli w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać więcej informacji na temat tworzenia puli platformy Apache Spark w obszarze roboczym usługi Azure Synapse Analytics, zobacz [Szybki Start: Tworzenie puli bezserwerowej Apache Spark przy użyciu programu Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Typ `attach_config` to `ComputeTargetAttachConfiguration` .

Po utworzeniu konfiguracji można utworzyć Uczenie maszynowe, `ComputeTarget` przekazując w `Workspace` , `ComputeTargetAttachConfiguration` i nazwę, za pomocą której chcesz odwołać się do obliczeń w obszarze roboczym Machine Learning. Wywołanie `ComputeTarget.attach()` jest asynchroniczne, więc przykładowe bloki do momentu zakończenia wywołania.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Utwórz `SynapseSparkStep` , który używa połączonej puli Apache Spark

Przykładowe [zadanie Spark notesu w puli platformy Apache Spark](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) definiuje prosty potok uczenia maszynowego. Najpierw Notes definiuje krok przygotowania danych, który jest obsługiwany przez `synapse_compute` zdefiniowane w poprzednim kroku. Następnie Notes definiuje krok szkoleniowy, który jest obsługiwany przez obiekt docelowy obliczeń lepiej dostosowany do szkoleń. W przykładowym notesie jest stosowana baza danych do przeżycia Titanic w celu zademonstrowania danych wejściowych i wyjściowych. w rzeczywistości nie czyści danych ani nie tworzą modelu predykcyjnego. Ze względu na to, że w tym przykładzie nie ma rzeczywistych szkoleń, krok szkoleniowy używa niedrogiego, opartego na PROCESORAch zasobów obliczeniowych.

Dane są przesyłane do potoku uczenia maszynowego za pomocą `DatasetConsumptionConfig` obiektów, które mogą przechowywać dane tabelaryczne lub zestawy plików. Dane często pochodzą z plików w magazynie obiektów BLOB w magazynie danych obszaru roboczego. Poniższy kod przedstawia typowy kod służący do tworzenia danych wejściowych dla potoku uczenia maszynowego:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

Powyższy kod założono, że plik `Titanic.csv` znajduje się w magazynie obiektów BLOB. Kod pokazuje, jak odczytać plik jako `TabularDataset` i `FileDataset` . Ten kod służy tylko do celów demonstracyjnych, ponieważ może być mylący dla zduplikowanych danych wejściowych lub do interpretowania pojedynczego źródła danych zarówno jako zasobu zawierającego tabelę, jak i jako plik.

> [!Important]
> Aby można było użyć `FileDataset` jako danych wejściowych, `azureml-core` wersja musi być co najmniej `1.20.0` . Sposób określenia tej metody przy użyciu `Environment` klasy opisano poniżej.

Po zakończeniu kroku można przechowywać dane wyjściowe przy użyciu kodu podobnego do:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

W takim przypadku dane byłyby przechowywane w `datastore` pliku o nazwie `test` i będą dostępne w obszarze roboczym uczenie maszynowego jako `Dataset` z nazwą `registered_dataset` .

Oprócz danych, krok potoku może mieć zależności w języku Python dla poszczególnych etapów. Poszczególne `SynapseSparkStep` obiekty mogą również określać precyzyjną konfigurację usługi Azure Synapse Apache Spark. Jest to pokazane w poniższym kodzie, który określa, że `azureml-core` wersja pakietu musi być co najmniej `1.20.0` . (Jak wspomniano wcześniej, to wymaganie `azureml-core` jest wymagane do użycia `FileDataset` jako dane wejściowe.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Powyższy kod określa pojedynczy krok w potoku usługi Azure Machine Learning. Ten krok `environment` określa określoną `azureml-core` wersję i może w razie potrzeby dodać inne zależności Conda lub PIP.

Plik `SynapseSparkStep` będzie zip i przekazywany z komputera lokalnego do podkatalogu `./code` . Ten katalog zostanie odtworzony na serwerze obliczeniowym, a krok uruchomi plik `dataprep.py` z tego katalogu. `inputs`I tego `outputs` kroku są opisane `step1_input1` `step1_input2` `step1_output` wcześniej obiekty, i. Najprostszym sposobem uzyskania dostępu do tych wartości w `dataprep.py` skrypcie jest skojarzenie ich z nazwami `arguments` .

Następny zestaw argumentów dla `SynapseSparkStep` kontrolki konstruktora Apache Spark. `compute_target`Jest to `'link1-spark01'` element, który został wcześniej dołączony jako cel obliczeniowy. Inne parametry określają pamięć i rdzenie, których chcesz użyć.

Przykładowy Notes używa następującego kodu dla `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Ten skrypt "Przygotowywanie danych" nie wykonuje żadnej rzeczywistej transformacji danych, ale pokazuje, jak pobrać dane, przekonwertować go na ramkę Dataframe i jak wykonać pewne podstawowe operacje Apache Spark. Dane wyjściowe można znaleźć w Azure Machine Learning Studio, otwierając uruchomienie podrzędne, wybierając kartę dane **wyjściowe + dzienniki** i otwierając `logs/azureml/driver/stdout` plik, jak pokazano na poniższej ilustracji.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Zrzut ekranu programu Studio przedstawiający kartę stdout przebiegu podrzędnego":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Używanie `SynapseSparkStep` w potoku

Inne kroki potoku mogą mieć własne unikatowe środowiska i uruchamiać je w różnych zasobach obliczeniowych odpowiednich dla danego zadania. Przykładowy Notes uruchamia "krok szkoleniowy" w małym klastrze CPU:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

W razie potrzeby w powyższym kodzie zostanie utworzony nowy zasób obliczeniowy. Następnie `step1_output` wynik zostanie przekonwertowany na dane wejściowe dla etapu szkolenia. `as_download()`Opcja oznacza, że dane będą przenoszone na zasób obliczeniowy, co powoduje szybszy dostęp. Jeśli dane były tak duże, że nie mieszczą się na lokalnym dysku twardym, można użyć `as_mount()` opcji przesyłania strumieniowego danych za pośrednictwem systemu plików bezpiecznik. `compute_target`Ten drugi krok to `'cpucluster'` , nie `'link1-spark01'` zasób użyty w kroku przygotowywania danych. W tym kroku jest używany prosty program `train.py` , a nie `dataprep.py` użyty w poprzednim kroku. Szczegóły można znaleźć `train.py` w notesie przykładowym.

Po zdefiniowaniu wszystkich kroków można utworzyć i uruchomić potok. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Powyższy kod tworzy potok składający się z kroku przygotowywania danych na Apache Spark pule obsługiwane przez usługę Azure Synapse Analytics ( `step_1` ) i Krok uczenia ( `step_2` ). Platforma Azure oblicza wykres wykonawczy, sprawdzając zależności danych między krokami. W takim przypadku istnieje tylko prosta zależność, która `step2_input` musi być wymagana `step1_output` .

Wywołanie w celu `pipeline.submit` utworzenia, w razie potrzeby, eksperyment o nazwie `synapse-pipeline` i asynchronicznie rozpoczyna przebieg w nim. Poszczególne kroki w potoku są uruchamiane jako podrzędne uruchomienia tego głównego przebiegu i można je monitorować i przeglądać na stronie eksperymenty w programie Studio.

## <a name="next-steps"></a>Następne kroki

* [Publikowanie i śledzenie potoków uczenia maszynowego](how-to-deploy-pipelines.md) 
* [Monitorowanie usługi Azure Machine Learning](monitor-azure-machine-learning.md)
* [Korzystanie z zautomatyzowanej tablicy w potoku Azure Machine Learning w języku Python](how-to-use-automlstep-in-pipelines.md)
