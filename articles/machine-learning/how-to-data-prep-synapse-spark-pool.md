---
title: Przetwarzanie danych z pulami Apache Spark (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak dołączać i uruchamiać pule Apache Spark danych przetwarzanie za pomocą usługi Azure Synapse Analytics i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 9ced4da7f71a0499e538e499644d89240611f1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956217"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Dołącz pule Apache Spark (obsługiwane przez usługę Azure Synapse Analytics) dla danych przetwarzanie (wersja zapoznawcza)

W tym artykule dowiesz się, jak dołączać i uruchamiać pulę Apache Spark, która jest obsługiwana przez [usługę Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) na potrzeby przetwarzanie danych na dużą skalę. 

Ten artykuł zawiera wskazówki dotyczące interaktywnego wykonywania zadań przetwarzanie danych w ramach dedykowanej sesji Synapse w notesie Jupyter. Jeśli wolisz używać potoków Azure Machine Learning, zobacz [jak używać Apache Spark (obsługiwane przez usługę Azure Synapse Analytics) w potoku uczenia maszynowego (wersja zapoznawcza)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> Azure Machine Learning i integracja z usługą Azure Synapse Analytics jest w wersji zapoznawczej. Możliwości przedstawione w tym artykule wykorzystują `azureml-synapse` pakiet, który zawiera [eksperymentalne](/python/api/overview/azure/ml/#stable-vs-experimental) funkcje w wersji zapoznawczej, które mogą ulec zmianie w dowolnym momencie.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Integracja Azure Machine Learning i Azure Synapse Analytics (wersja zapoznawcza)

Integracja usługi Azure Synapse Analytics z usługą Azure Machine Learning (wersja zapoznawcza) umożliwia dołączenie puli Apache Spark z użyciem usługi Azure Synapse do interaktywnej eksploracji i przygotowania danych. Dzięki tej integracji można korzystać z dedykowanych obliczeń dla przetwarzanie danych na dużą skalę, a wszystko to w ramach tego samego notesu Python używanego do uczenia modeli uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Utwórz obszar roboczy usługi Azure Synapse Analytics w Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Tworzenie puli Apache Spark przy użyciu Azure Portal, narzędzi sieci Web lub Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning](concept-compute-instance.md#create) z już zainstalowanym zestawem SDK. 

* Zainstaluj `azureml-synapse` pakiet (wersja zapoznawcza) przy użyciu następującego kodu:

  ```python
  pip install azureml-synapse
  ```

* [Połącz Azure Machine Learning obszar roboczy i obszar roboczy analizy usługi Azure Synapse](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Pobierz istniejącą połączoną usługę
Zanim będzie możliwe dołączenie dedykowanego obliczenia dla przetwarzanie danych, musisz mieć obszar roboczy ML połączony z obszarem roboczym usługi Azure Synapse Analytics, który jest określany jako połączona usługa. 

Do pobrania i użycia istniejącej połączonej usługi wymagane są uprawnienia **użytkownika lub współautora** do obszaru roboczego usługi Azure Synapse Analytics.

Wyświetl wszystkie połączone usługi powiązane z obszarem roboczym usługi Machine Learning. 

```python
LinkedService.list(ws)
```

Ten przykład umożliwia pobranie istniejącej połączonej usługi, `synapselink1` z obszaru roboczego, `ws` przy użyciu [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metody.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Dołącz pulę Synapse Spark jako obliczenie

Po pobraniu połączonej usługi Dołącz pulę Apache Spark Synapse jako dedykowany zasób obliczeniowy dla zadań przetwarzanie danych. 

Pule Apache Spark można dołączać za pośrednictwem programu,
* Studio uczenia maszynowego Azure
* [Szablony usługi Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Zestaw SDK języka Python 

### <a name="attach-a-pool-via-the-studio"></a>Dołączanie puli za pośrednictwem programu Studio
Wykonaj następujące kroki: 

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. W sekcji **Zarządzanie** w okienku po lewej stronie wybierz pozycję **połączone usługi** .
1. Wybierz obszar roboczy Synapse.
1. Wybierz pozycję **dołączone pule platformy Spark** w lewym górnym rogu. 
1. Wybierz pozycję **Dołącz**. 
1. Wybierz z listy pulę Apache Spark i podaj nazwę.  
    1. Ta lista zawiera listę dostępnych pul Synapse Spark, które mogą być dołączone do obliczeń. 
    1. Aby utworzyć nową pulę Synapse Spark, zobacz [Tworzenie puli Apache Spark za pomocą Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Wybierz pozycję **Dołącz zaznaczone**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Dołączanie puli przy użyciu zestawu SDK języka Python

Do dołączania puli Apache Spark można również użyć **zestawu SDK języka Python** . 

Kod Obserwuj, 
1. Konfiguruje SynapseCompute za pomocą,

   1. LinkedService, `linked_service` który został utworzony lub pobrany w poprzednim kroku. 
   1. Typ elementu docelowego obliczeń, który ma zostać dołączony. `SynapseSpark`
   1. Nazwa puli Apache Spark. Musi on być zgodny z istniejącą pulą Apache Spark, która znajduje się w obszarze roboczym usługi Azure Synapse Analytics.
   
1. Tworzy ComputeTarget uczenia maszynowego, przekazując, 
   1. Obszar roboczy uczenia maszynowego, którego chcesz użyć, `ws`
   1. Nazwa, do której chcesz odwołać się do obliczeń w obszarze roboczym Azure Machine Learning. 
   1. Attach_configuration określony podczas konfigurowania obliczeń Synapse.
       1. Wywołanie metody ComputeTarget. Attach () jest asynchroniczne, więc przykładowe bloki do momentu zakończenia wywołania.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Sprawdź, czy pula Apache Spark jest dołączona.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Uruchamianie puli Synapse Spark na potrzeby zadań przygotowywania danych

Aby rozpocząć przygotowywanie danych z pulą Apache Spark, określ nazwę puli Apache Spark:

> [!IMPORTANT]
> Aby nadal korzystać z puli Apache Spark należy wskazać, który zasób obliczeniowy ma być używany w ramach zadań przetwarzanie danych w `%synapse` przypadku pojedynczych wierszy kodu i `%%synapse` dla wielu wierszy. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Po rozpoczęciu sesji można sprawdzić metadane sesji.

```python
%synapse meta
```

Możesz określić [środowisko Azure Machine Learning](concept-environments.md) , które ma być używane podczas sesji Apache Spark. Tylko zależności Conda określone w środowisku zaczną obowiązywać. Obraz platformy Docker nie jest obsługiwany.

>[!WARNING]
>  Zależności języka Python określone w zależnościach Conda środowiska nie są obsługiwane w pulach Apache Spark. Obecnie obsługiwane są tylko ustalone wersje języka Python. Sprawdź wersję języka Python, dołączając ją  `sys.version_info` do skryptu.

Poniższy kod tworzy środowisko, `myenv` które instaluje `azureml-core` wersję 1.20.0 i `numpy` wersja 1.17.0 przed rozpoczęciem sesji. Następnie można uwzględnić to środowisko w instrukcji Apache Spark Session `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Aby rozpocząć przygotowywanie danych za pomocą puli Apache Spark i środowiska niestandardowego, określ nazwę puli Apache Spark i środowisko, które ma być używane podczas sesji Apache Spark. Ponadto możesz podać identyfikator subskrypcji, grupę zasobów obszaru roboczego usługi Machine Learning i nazwę obszaru roboczego usługi Machine Learning.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Ładowanie danych z magazynu

Po rozpoczęciu sesji Apache Spark zapoznaj się z danymi, które chcesz przygotować. Ładowanie danych jest obsługiwane w usłudze Azure Blob Storage i w Azure Data Lake Storage generacjach 1 i 2.

Istnieją dwa sposoby ładowania danych z tych usług magazynu: 

* Bezpośrednio Ładuj dane z magazynu przy użyciu ścieżki systemu plików rozproszonych Hadoop (HDFS).

* Odczytaj dane z istniejącego [Azure Machine Learning zestawu danych](how-to-create-register-datasets.md).

Aby uzyskać dostęp do tych usług magazynu, potrzebne są uprawnienia **czytnika danych obiektu blob magazynu** . Jeśli planujesz napisać dane z powrotem do tych usług magazynu, potrzebujesz uprawnień **współautor danych obiektów blob magazynu** . [Dowiedz się więcej o uprawnieniach i rolach magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Ładowanie danych za pomocą ścieżki systemu plików rozproszonych Hadoop (HDFS)

Aby załadować i odczytać dane z magazynu z odpowiednią ścieżką systemu plików HDFS, należy udostępnić poświadczenia uwierzytelniania dostępu do danych. Te poświadczenia różnią się w zależności od typu magazynu.  

Poniższy kod ilustruje sposób odczytywania danych z **usługi Azure Blob Storage** do ramki Dataframe z użyciem tokenu sygnatury dostępu współdzielonego (SAS) lub klucza dostępu. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Poniższy kod ilustruje sposób odczytywania danych z programu **Azure Data Lake Storage Generation 1 (ADLS Gen 1)** przy użyciu poświadczeń jednostki usługi. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Poniższy kod ilustruje sposób odczytywania danych z programu **Azure Data Lake Storage Generation 2 (ADLS Gen 2)** przy użyciu poświadczeń nazwy głównej usługi. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Odczytaj dane z zarejestrowanych zestawów danych

Możesz również uzyskać istniejący zarejestrowany zestaw danych w obszarze roboczym i wykonać na nim Przygotowywanie danych, konwertując go na ramkę Dataframe.

Poniższy przykład uwierzytelnia się w obszarze roboczym, pobiera zarejestrowaną TabularDataset, `blob_dset` która odwołuje się do plików w magazynie obiektów blob, i konwertuje ją na ramkę danych Spark. W przypadku konwertowania zestawów danych do ramki Dataframe platformy Spark można korzystać `pyspark` z bibliotek eksploracji i przygotowania.  

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

## <a name="perform-data-wrangling-tasks"></a>Wykonywanie zadań przetwarzanie danych

Po pobraniu i zbadaniu danych można wykonywać zadania przetwarzanie danych.

Poniższy kod, rozwija się w przykładzie HDFS w poprzedniej sekcji i filtruje dane w ramce Dataframe, `df` na podstawie kolumny i grup **pozostających** w **wieku**

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Zapisz dane w magazynie i Zatrzymaj sesję platformy Spark

Po zakończeniu eksploracji i przygotowania danych Przechowaj przygotowane dane do późniejszego użycia na koncie magazynu na platformie Azure.

W poniższym przykładzie przygotowane dane są zapisywane z powrotem do usługi Azure Blob Storage i zastępują oryginalny `Titanic.csv` plik w `training_data` katalogu. Aby zapisać z powrotem do magazynu, potrzebne są uprawnienia **współautora danych obiektu blob magazynu** . [Dowiedz się więcej o uprawnieniach i rolach magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Po zakończeniu przygotowywania danych i zapisaniu w magazynie przygotowanego danych Zatrzymaj korzystanie z puli Apache Spark przy użyciu poniższego polecenia.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Utwórz zestaw danych reprezentujący przygotowane dane

Gdy wszystko będzie gotowe do korzystania z przygotowanych danych na potrzeby szkolenia modeli, Połącz się z magazynem za pomocą [magazynu Azure Machine Learning datastore](how-to-access-data.md)i określ, które pliki mają być używane z [Azure Machine Learning zestawem danych](how-to-create-register-datasets.md).

Poniższy przykład kodu,

* Zakłada, że został już utworzony magazyn danych, który łączy się z usługą magazynu, w której zapisano przygotowane dane.  
* Pobiera istniejący magazyn danych, `mydatastore` z obszaru roboczego, `ws` za pomocą metody get ().
* Tworzy element [FileDataset](how-to-create-register-datasets.md#filedataset), `train_ds` który odwołuje się do przygotowanych plików danych znajdujących się w `training_data` katalogu w `mydatastore` .  
* Tworzy zmienną `input1` , której można użyć w późniejszym czasie w celu udostępnienia plików danych dla `train_ds` elementu docelowego obliczeń.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Przykładowy notes

Zapoznaj [się z tym kompleksowym notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) , aby zapoznać się z szczegółowym przykładem dotyczącym sposobu wykonywania przygotowywania i wybierania modeli z jednego notesu za pomocą usługi Azure Synapse Analytics i Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu](how-to-set-up-training-targets.md).
* [Uczenie z zestawem danych Azure Machine Learning](how-to-train-with-datasets.md)
