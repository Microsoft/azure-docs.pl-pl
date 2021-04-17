---
title: Używanie Apache Spark w potoku uczenia maszynowego (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Połącz obszar Azure Synapse Analytics z potokiem uczenia maszynowego platformy Azure, aby używać Apache Spark do manipulowania danymi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576384"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Jak używać aplikacji Apache Spark (obsługiwanych przez Azure Synapse Analytics) w potoku uczenia maszynowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak używać pul Apache Spark obsługiwanych przez usługę Azure Synapse Analytics jako docelowego obiektu obliczeniowego dla kroku przygotowywania danych w potoku Azure Machine Learning danych. Dowiesz się, w jaki sposób pojedynczy potok może używać zasobów obliczeniowych odpowiednich dla konkretnego kroku, takiego jak przygotowywanie lub szkolenie danych. Zobaczysz, jak dane są przygotowywane na krok spark i jak są przekazywane do następnego kroku. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz obszar [Azure Machine Learning do](how-to-manage-workspace.md) przechowywania wszystkich zasobów potoku.

* [Skonfiguruj środowisko projektowe,](how-to-configure-environment.md) aby zainstalować Azure Machine Learning SDK lub użyć Azure Machine Learning [obliczeniowego](concept-compute-instance.md) z już zainstalowanym zestawem SDK.

* Utwórz obszar Azure Synapse Analytics i pulę Apache Spark (zobacz Szybki [start:](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)tworzenie puli Apache Spark serwera przy użyciu Synapse Studio ). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Łączenie obszaru Azure Machine Learning i Azure Synapse Analytics roboczego 

Możesz tworzyć pule Apache Spark i administrować tymi pulami w Azure Synapse Analytics roboczym. Aby zintegrować Apache Spark z obszarem roboczym Azure Machine Learning, musisz połączyć się z Azure Synapse Analytics [roboczym.](how-to-link-synapse-ml-workspaces.md) 

Pulę zasobów można Apache Spark za pośrednictwem Azure Machine Learning studio użytkownika przy użyciu **strony Połączone** usługi. Możesz to również zrobić za pośrednictwem **strony Obliczenia** z **opcją Dołącz obliczenia.**

Możesz również dołączyć pulę Apache Spark za pomocą zestawu SDK (zgodnie z poniższymi instrukcjami) lub za pośrednictwem szablonu USŁUGI ARM (zobacz ten [przykładowy szablon arm).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json) 

Możesz użyć wiersza polecenia, aby postępować zgodnie z szablonem usługi ARM, dodać powiązaną usługę i dołączyć Apache Spark puli przy użyciu następującego kodu:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Aby pomyślnie utworzyć link do Azure Synapse Analytics roboczego, musisz mieć rolę właściciela w zasobie Azure Synapse Analytics obszaru roboczego. Sprawdź dostęp w Azure Portal.
> Połączona usługa po utworzeniu otrzyma tożsamość przypisaną przez system (IT). Musisz przypisać tej usłudze linku rolę "Synapse Apache Spark administrator" z usługi Synapse Studio, aby można było przesłać zadanie Spark (zobacz Jak zarządzać przypisaniami ról RBAC usługi Synapse w [programie Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Musisz również nadać użytkownikowi obszaru roboczego Azure Machine Learning rolę "Współautor" z Azure Portal zarządzania zasobami.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Tworzenie lub pobieranie połączenia między obszarem roboczym Azure Synapse Analytics a obszarem roboczym Azure Machine Learning roboczego

Połączone usługi w obszarze roboczym można pobrać za pomocą kodu, takiego jak:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Najpierw uzyskuje dostęp do swojego Azure Machine Learning roboczego przy użyciu konfiguracji w programie (zobacz Samouczek: rozpoczynanie pracy z Azure Machine Learning `Workspace.from_config()` `config.json` w [środowisku projektowym).](tutorial-1st-experiment-sdk-setup-local.md) Następnie kod drukuje wszystkie połączone usługi dostępne w obszarze roboczym. Na koniec `LinkedService.get()` pobiera połączona usługę o nazwie `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Dołączanie puli platformy Apache Spark jako docelowego obiektu obliczeniowego dla platformy Azure Machine Learning

Aby użyć puli apache spark do zasilania kroku w potoku uczenia maszynowego, musisz dołączyć go jako krok potoku, jak pokazano w `ComputeTarget` poniższym kodzie.

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

Pierwszym krokiem jest `SynapseCompute` skonfigurowanie . Argument `linked_service` jest `LinkedService` obiektem utworzonym lub pobranym w poprzednim kroku. Argumentem `type` musi być `SynapseSpark` . `pool_name`Argument w pliku musi `SynapseCompute.attach_configuration()` odpowiadać istniejącej puli w twoim Azure Synapse Analytics roboczym. Aby uzyskać więcej informacji na temat tworzenia puli platformy Apache Spark w obszarze roboczym usługi Azure Synapse Analytics, zobacz Szybki [start:](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)tworzenie bez serwera Apache Spark przy użyciu Synapse Studio . Typ to `attach_config` `ComputeTargetAttachConfiguration` .

Po utworzeniu konfiguracji utworzysz uczenie maszynowe, przekazując wartości , i nazwę, za pomocą której chcesz odwoływać się do zasobów obliczeniowych w obszarze roboczym `ComputeTarget` `Workspace` uczenia `ComputeTargetAttachConfiguration` maszynowego. Wywołanie metody jest `ComputeTarget.attach()` asynchroniczne, więc przykładowe bloki są blokami aż do zakończenia wywołania.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Tworzenie `SynapseSparkStep` puli, która używa połączonej Apache Spark puli

Przykładowe zadanie [spark notesu w puli Apache Spark](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) definiuje prosty potok uczenia maszynowego. Najpierw notes definiuje krok przygotowania danych obsługiwany przez krok `synapse_compute` zdefiniowany w poprzednim kroku. Następnie notes definiuje krok trenowania obsługiwany przez docelowy obiekt obliczeniowy lepiej dopasowany do trenowania. Przykładowy notes używa bazy danych titanica do zademonstrowania danych wejściowych i wyjściowych; Nie czyści danych ani nie robi modelu predykcyjnego. Ponieważ w tym przykładzie nie ma rzeczywistego szkolenia, w kroku trenowania jest używany niedrogi zasób obliczeniowy oparty na procesorze CPU.

Dane przepływają do potoku uczenia maszynowego za pomocą obiektów, które mogą przechowywać dane tabelaryce `DatasetConsumptionConfig` lub zestawy plików. Dane często pochodzą z plików w magazynie obiektów blob w magazynie danych obszaru roboczego. Poniższy kod przedstawia typowy kod tworzenia danych wejściowych dla potoku uczenia maszynowego:

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

Powyższy kod zakłada, że plik znajduje `Titanic.csv` się w magazynie obiektów blob. Kod pokazuje, jak odczytać plik jako i `TabularDataset` jako `FileDataset` . Ten kod jest tylko dla celów demonstracyjnych, ponieważ duplikowanie danych wejściowych lub interpretowanie pojedynczego źródła danych jako zasobu zawierającego tabelę i po prostu pliku byłoby mylące.

> [!Important]
> Aby można było użyć jako `FileDataset` danych wejściowych, twoja `azureml-core` wersja musi mieć co najmniej . `1.20.0` Sposób określania tego przy użyciu `Environment` klasy został omówiony poniżej.

Po zakończeniu kroku można przechowywać dane wyjściowe przy użyciu kodu podobnego do:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

W takim przypadku dane będą przechowywane w pliku o nazwie i będą dostępne w obszarze roboczym uczenia maszynowego `datastore` jako dane o nazwie `test` `Dataset` `registered_dataset` .

Oprócz danych krok potoku może mieć zależności języka Python dla 1 kroku. Poszczególne `SynapseSparkStep` obiekty mogą również określać Azure Synapse Apache Spark konfiguracji. Jest to pokazane w poniższym kodzie, który określa, że wersja `azureml-core` pakietu musi być co najmniej `1.20.0` . (Jak wspomniano wcześniej, to wymaganie dla funkcji jest wymagane do `azureml-core` użycia jako `FileDataset` danych wejściowych).

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

Powyższy kod określa pojedynczy krok w potoku uczenia maszynowego platformy Azure. Ten krok określa określoną wersję i w razie potrzeby może dodać inne zależności `environment` `azureml-core` conda lub pip.

Plik `SynapseSparkStep` zip i przekaże z komputera lokalnego podkatalog `./code` . Ten katalog zostanie ponownie utworzony na serwerze obliczeniowym, a krok spowoduje uruchomienie pliku `dataprep.py` z tego katalogu. I `inputs` `outputs` tego kroku to obiekty , i `step1_input1` `step1_input2` `step1_output` omówione wcześniej. Najprostszym sposobem uzyskania dostępu do tych wartości w `dataprep.py` skrypcie jest skojarzenie ich z nazwą `arguments` .

Następny zestaw argumentów do `SynapseSparkStep` kontrolki konstruktora Apache Spark. Element `compute_target` jest `'link1-spark01'` elementem, który wcześniej dołączono jako docelowy obiekt obliczeniowy. Inne parametry określają pamięć i rdzenie, których chcemy użyć.

Przykładowy notes używa następującego kodu dla `dataprep.py` :

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

Ten skrypt "przygotowywania danych" nie robi żadnych rzeczywistych przekształceń danych, ale ilustruje sposób pobierania danych, konwertowania ich na ramę danych platformy Spark i wykonywania niektórych podstawowych Apache Spark danych. Dane wyjściowe w programie Azure Machine Learning Studio można znaleźć, otwierając przebieg podrzędny, wybierając kartę Dane wyjściowe **i** dzienniki i otwierając plik, jak pokazano na `logs/azureml/driver/stdout` poniższej ilustracji.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Zrzut ekranu przedstawiający kartę stdout uruchomienia podrzędnego w programie Studio":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Używanie w `SynapseSparkStep` potoku

W poniższym przykładzie użyto danych wyjściowych `SynapseSparkStep` z sekcji utworzonej w [poprzedniej sekcji](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool). Inne kroki w potoku mogą mieć własne unikatowe środowiska i być uruchamiane w różnych zasobach obliczeniowych odpowiednich dla zadania. Przykładowy notes uruchamia "krok trenowania" w małym klastrze procesora CPU:

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

Powyższy kod tworzy nowy zasób obliczeniowy w razie potrzeby. Następnie wynik `step1_output` jest konwertowany na dane wejściowe dla kroku trenowania. Opcja `as_download()` oznacza, że dane zostaną przeniesione do zasobu obliczeniowego, co spowoduje szybszy dostęp. Jeśli dane były tak duże, że nie mieściłyby się na lokalnym obliczeniowym dysku twardym, można użyć opcji przesyłania strumieniowego danych za pośrednictwem `as_mount()` systemu plików FUSE. Drugi krok to , a nie zasób użyty `compute_target` w kroku przygotowywania `'cpucluster'` `'link1-spark01'` danych. W tym kroku jest używany prosty program, a nie program używany `train.py` `dataprep.py` w poprzednim kroku. Szczegółowe informacje można znaleźć w `train.py` przykładowym notesie.

Po utworzeniu wszystkich kroków możesz utworzyć i uruchomić potok. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Powyższy kod tworzy potok składający się z kroku przygotowywania danych w pulach Apache Spark obsługiwanych przez Azure Synapse Analytics ( ) i `step_1` kroku trenowania ( `step_2` ). Platforma Azure oblicza wykres wykonywania, sprawdzając zależności danych między krokami. W tym przypadku istnieje tylko prosta zależność, która `step2_input` zawsze wymaga . `step1_output`

Wywołanie metody `pipeline.submit` tworzy, w razie potrzeby, eksperyment o nazwie i asynchronicznie rozpoczyna w nim `synapse-pipeline` przebieg. Poszczególne kroki w potoku są uruchamiane jako podrzędne przebiegi tego głównego uruchomienia i można je monitorować i przeglądać na stronie Experiments (Eksperymenty) programu Studio.

## <a name="next-steps"></a>Następne kroki

* [Publikowanie i śledzenie potoków uczenia maszynowego](how-to-deploy-pipelines.md) 
* [Monitorowanie usługi Azure Machine Learning](monitor-azure-machine-learning.md)
* [Używanie zautomatyzowanego uczenia maszynowego w potoku Azure Machine Learning w języku Python](how-to-use-automlstep-in-pipelines.md)
