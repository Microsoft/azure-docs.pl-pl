---
title: Przetwarzanie danych przy użyciu pul Apache Spark (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak dołączać i uruchamiać pule Apache Spark danych na Azure Synapse Analytics i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 1852bfdb4bf8513aaa5d43993e2b6ff804808dbd
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575653"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Dołączanie Apache Spark (obsługiwanych przez Azure Synapse Analytics) w celu przetwarzania danych (wersja zapoznawcza)

Z tego artykułu dowiesz się, jak dołączyć pulę Apache Spark obsługiwanych przez usługę [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) do obszaru roboczego usługi Azure Machine Learning, aby można było ją uruchomić i wykonywać rozmieszczanie danych na dużą skalę. 

Ten artykuł zawiera wskazówki dotyczące interakcyjnego wykonywania zadań związanych z rozmieszczaniem danych w dedykowanej sesji usługi Synapse w notesie Jupyter przy użyciu zestawu SDK języka [Python Azure Machine Learning języka Python.](/python/api/overview/azure/ml/) Jeśli wolisz używać potoków Azure Machine Learning, zobacz Jak używać potoków Apache Spark (obsługiwanych przez Azure Synapse Analytics) w potoku uczenia maszynowego [(wersja zapoznawcza).](how-to-use-synapsesparkstep.md)

Jeśli szukasz wskazówek dotyczących korzystania z aplikacji Azure Synapse Analytics z obszarem roboczym synapse, zobacz serię [Azure Synapse Analytics wprowadzenie.](../synapse-analytics/get-started.md)

>[!IMPORTANT]
> Integracja Azure Machine Learning i Azure Synapse Analytics jest w wersji zapoznawczej. Możliwości przedstawione w tym artykule wykorzystują pakiet zawierający eksperymentalne funkcje w wersji `azureml-synapse` zapoznawczej, które mogą ulec zmianie w dowolnym momencie. [](/python/api/overview/azure/ml/#stable-vs-experimental)

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning i Azure Synapse Analytics (wersja zapoznawcza)

Integracja Azure Synapse Analytics z usługą Azure Machine Learning (wersja zapoznawcza) umożliwia dołączanie puli Apache Spark z programem Azure Synapse do interaktywnej eksploracji i przygotowywania danych. Dzięki tej integracji możesz mieć dedykowane zasoby obliczeniowe do rozmieszczania danych na dużą skalę, a wszystko to w tym samym notesie języka Python, który jest przeznaczony do trenowania modeli uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstalowany [Azure Machine Learning SDK języka Python.](/python/api/overview/azure/ml/install) 

* [Utwórz obszar roboczy Azure Machine Learning roboczego.](how-to-manage-workspace.md?tabs=python)

* [Utwórz obszar roboczy Azure Synapse Analytics w Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Tworzenie Apache Spark przy użyciu Azure Portal, narzędzi internetowych lub Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Skonfiguruj środowisko projektowe,](how-to-configure-environment.md) aby zainstalować Azure Machine Learning SDK lub użyć Azure Machine Learning [obliczeniowego](concept-compute-instance.md#create) z już zainstalowanym zestawem SDK. 

* Zainstaluj `azureml-synapse` pakiet (wersja zapoznawcza) przy użyciu następującego kodu:

  ```python
  pip install azureml-synapse
  ```

* [Połącz Azure Machine Learning i Azure Synapse Analytics obszaru roboczego.](how-to-link-synapse-ml-workspaces.md)

## <a name="get-an-existing-linked-service"></a>Uzyskiwanie istniejącej połączonej usługi
Zanim będzie można dołączyć dedykowane zasoby obliczeniowe do obsługi przetwarzania danych, musisz mieć obszar roboczy uczenia maszynowego połączony z obszarem roboczym usługi Azure Synapse Analytics, który jest określany jako połączona usługa. 

Pobieranie i używanie istniejącej połączonej usługi **wymaga** uprawnień użytkownika lub współautora do Azure Synapse Analytics roboczego.

Wyświetl wszystko połączone usługi skojarzone z obszarem roboczym uczenia maszynowego. 

```python
from azureml.core import LinkedService

LinkedService.list(ws)
```

W tym przykładzie jest pobierana istniejąca połączona usługa z `synapselink1` obszaru roboczego `ws` za pomocą metody [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) .
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Dołączanie puli synapse Spark jako zasobów obliczeniowych

Po pobraniu połączonej usługi dołącz pulę usługi Synapse Apache Spark jako dedykowany zasób obliczeniowy dla zadań przetwarzania danych. 

Możesz dołączyć Apache Spark pule za pomocą:
* Studio uczenia maszynowego Azure
* [Szablony usługi Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Zestaw SDK Azure Machine Learning Python 

### <a name="attach-a-pool-via-the-studio"></a>Dołączanie puli za pośrednictwem programu Studio
Wykonaj następujące kroki: 

1. Zaloguj się do [Azure Machine Learning studio](https://ml.azure.com/).
1. Wybierz **pozycję Połączone usługi** w sekcji **Zarządzanie** w okienku po lewej stronie.
1. Wybierz obszar roboczy synapse.
1. Wybierz **pozycję Dołączone pule platformy Spark** w lewym górnym rogu. 
1. Wybierz pozycję **Dołącz**. 
1. Wybierz pulę Apache Spark z listy i podaj nazwę.  
    1. Ta lista identyfikuje dostępne pule synapse Spark, które można dołączyć do obliczeń. 
    1. Aby utworzyć nową pulę synapse Spark, zobacz [Tworzenie Apache Spark przy użyciu Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Wybierz pozycję **Dołącz wybraną pozycję**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Dołączanie puli przy użyciu zestawu SDK języka Python

Możesz również użyć zestawu **SDK języka Python,** aby dołączyć Apache Spark pulę. 

Postępuj zgodnie z kodem: 
1. Konfiguruje za [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) pomocą,

   1. , [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice) `linked_service` który został utworzony lub pobrany w poprzednim kroku. 
   1. Typ docelowego obiektu obliczeniowego, który chcesz dołączyć `SynapseSpark`
   1. Nazwa puli Apache Spark puli. Musi to być zgodne z istniejącą Apache Spark, która znajduje się w Azure Synapse Analytics roboczym.
   
1. Tworzy uczenie [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) maszynowe, przekazując 
   1. Obszar roboczy uczenia maszynowego, którego chcesz użyć `ws`
   1. Nazwa, która ma odwoływać się do zasobów obliczeniowych w Azure Machine Learning roboczym. 
   1. Adres attach_configuration określony podczas konfigurowania wystąpienia obliczeniowego synapse.
       1. Wywołanie metody ComputeTarget.attach() jest asynchroniczne, więc przykładowe bloki są wykonywane do momentu zakończenia wywołania.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Sprawdź, Apache Spark jest dołączona pula.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Uruchamianie puli synapse Spark dla zadań związanych z rozmieszczaniem danych

Aby rozpocząć przygotowywanie danych Apache Spark puli, określ Apache Spark nazwę puli:

> [!IMPORTANT]
> Aby nadal korzystać z puli Apache Spark, należy wskazać zasób obliczeniowy do użycia w ramach zadań związanych z rozmieszczaniem danych za pomocą elementu dla pojedynczych wierszy kodu i dla `%synapse` `%%synapse` wielu wierszy. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Po zakończeniu sesji możesz sprawdzić metadane sesji.

```python
%synapse meta
```

Można określić środowisko [Azure Machine Learning do](concept-environments.md) użycia podczas sesji Apache Spark użytkownika. Zostaną wprowadzone tylko zależności środowiska Conda określone w środowisku. Obraz platformy Docker nie jest obsługiwany.

>[!WARNING]
>  Zależności języka Python określone w zależnościach środowiska Conda nie są obsługiwane Apache Spark pulach. Obecnie obsługiwane są tylko stałe wersje języka Python. Sprawdź wersję języka Python, uwzględniając  `sys.version_info` w skrypcie.

Poniższy kod tworzy środowisko , które instaluje wersję `myenv` `azureml-core` 1.20.0 i `numpy` wersję 1.17.0 przed rozpoczęciem sesji. Następnie możesz uwzględnić to środowisko w Apache Spark `start` sesji.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Aby rozpocząć przygotowywanie danych przy użyciu Apache Spark i środowiska niestandardowego, określ nazwę Apache Spark i środowisko, które ma być Apache Spark sesji. Ponadto możesz podać identyfikator subskrypcji, grupę zasobów obszaru roboczego uczenia maszynowego i nazwę obszaru roboczego uczenia maszynowego.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Ładowanie danych z magazynu

Po Apache Spark sesji odczytuj dane, które chcesz przygotować. Ładowanie danych jest obsługiwane w przypadku usługi Azure Blob Storage i Azure Data Lake Storage Generacje 1 i 2.

Istnieją dwa sposoby ładowania danych z tych usług magazynu: 

* Bezpośrednie ładowanie danych z magazynu przy użyciu ścieżki rozproszonego systemu plików Hadoop (HDFS).

* Odczytywanie danych z istniejącego [zestawu Azure Machine Learning danych.](how-to-create-register-datasets.md)

Aby uzyskać dostęp do tych usług magazynu, musisz mieć uprawnienia **czytelnika danych obiektu blob** usługi Storage. Jeśli planujesz zapisywać dane z powrotem w tych usługach magazynu, musisz mieć uprawnienia Współautor danych obiektu blob usługi **Storage.** [Dowiedz się więcej o uprawnieniach i rolach magazynu.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Ładowanie danych za pomocą ścieżki rozproszonego systemu plików Hadoop (HDFS)

Aby załadować i odczytać dane z magazynu przy użyciu odpowiedniej ścieżki systemu plików HDFS, musisz mieć łatwo dostępne poświadczenia uwierzytelniania dostępu do danych. Te poświadczenia różnią się w zależności od typu magazynu.  

Poniższy kod przedstawia sposób odczytywania danych z usługi **Azure Blob Storage** do ramki danych platformy Spark przy użyciu tokenu sygnatury dostępu współdzielonego (SAS) lub klucza dostępu. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Poniższy kod przedstawia sposób odczytywania danych z usługi **Azure Data Lake Storage generacji 1 (ADLS Gen 1)** przy użyciu poświadczeń jednostki usługi. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Poniższy kod przedstawia sposób odczytywania danych z usługi **Azure Data Lake Storage generacji 2 (ADLS Gen 2)** przy użyciu poświadczeń jednostki usługi. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Odczytywanie danych z zarejestrowanych zestawów danych

Możesz również pobrać istniejący zarejestrowany zestaw danych w obszarze roboczym i wykonać na nim przygotowywanie danych, konwertując go na ramce danych platformy Spark.

Poniższy przykład służy do uwierzytelniania w obszarze roboczym, pobiera zarejestrowany zestaw danych TabularDataset, , który odwołuje się do plików w magazynie obiektów blob i konwertuje go na ramkę danych `blob_dset` platformy Spark. Podczas konwertowania zestawów danych na ramce danych platformy Spark można korzystać z `pyspark` bibliotek eksploracji i przygotowywania danych.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Wykonywanie zadań związanych z rozmieszczaniem danych

Po pobraniu i eksplorowania danych można wykonywać zadania związane z rozmieszczaniem danych.

Poniższy kod rozszerza przykład systemu plików HDFS w poprzedniej sekcji i filtruje dane w ramce danych platformy Spark, , na podstawie `df` **kolumny Ocaleni** i grup, które są na liście według **wieku**

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Zapisywanie danych w magazynie i zatrzymywanie sesji platformy Spark

Po zakończeniu eksplorowania i przygotowywania danych przechowuj przygotowane dane do późniejszego użycia na koncie magazynu na platformie Azure.

W poniższym przykładzie przygotowane dane są zapisywane z powrotem w usłudze Azure Blob Storage i zastępują oryginalny `Titanic.csv` plik w `training_data` katalogu. Aby zapisywać z powrotem w magazynie, musisz mieć uprawnienia **Współautor danych obiektu blob usługi Storage.** [Dowiedz się więcej o uprawnieniach i rolach magazynu.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Po zakończeniu przygotowywania danych i zapisaniu przygotowanych danych w magazynie zatrzymaj korzystanie z puli Apache Spark za pomocą następującego polecenia.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Tworzenie zestawu danych reprezentującego przygotowane dane

Gdy wszystko będzie gotowe do użycia przygotowanych danych do trenowania modelu, połącz się z magazynem przy użyciu magazynu danych usługi [Azure Machine Learning](how-to-access-data.md)i określ, które pliki mają być używane z zestawem danych Azure Machine Learning [danych.](how-to-create-register-datasets.md)

Poniższy przykład kodu:

* Przyjęto założenie, że utworzono już magazyn danych, który łączy się z usługą magazynu, w której zapisano przygotowane dane.  
* Pobiera ten istniejący magazyn danych `mydatastore` z obszaru roboczego za `ws` pomocą metody get().
* Tworzy zestaw [FileDataset](how-to-create-register-datasets.md#filedataset), , który odwołuje się do przygotowanych plików danych znajdujących `train_ds` się w katalogu w katalogu `training_data` `mydatastore` .  
* Tworzy zmienną , której można użyć w późniejszym czasie, aby udostępnić pliki danych zestawu danych `input1` `train_ds` dla docelowego obiektu obliczeniowego.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```
## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Przesyłanie przebiegu `ScriptRunConfig` eksperymentu do puli synapse Spark przy użyciu funkcji

Możesz również użyć dołączonego wcześniej klastra [Usługi Synapse Spark](#attach-a-pool-with-the-python-sdk) jako docelowego obiektu obliczeniowego do przesyłania przebiegu eksperymentu za pomocą obiektu [ScriptRunConfig.](/python/api/azureml-core/azureml.core.scriptrunconfig)

```Python
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--tabular_input", input1, 
                                                 "--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Po `ScriptRunConfig` skonfigurowaniu obiektu możesz przesłać przebieg.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```
Aby uzyskać dodatkowe informacje, takie `dataprep.py` jak skrypt używany w tym przykładzie, zobacz [przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb).

## <a name="example-notebooks"></a>Przykładowe notesy

Zobacz ten [przykładowy notes,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb) aby uzyskać więcej koncepcji i pokazów Azure Synapse Analytics i Azure Machine Learning integracji.

## <a name="next-steps"></a>Następne kroki

* [Trenowanie modelu.](how-to-set-up-training-targets.md)
* [Trenuj przy użyciu Azure Machine Learning danych](how-to-train-with-datasets.md)
