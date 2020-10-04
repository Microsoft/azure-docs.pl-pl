---
title: Konfigurowanie szkoleń & cele obliczeń wnioskowania
titleSuffix: Azure Machine Learning
description: Dodaj zasoby obliczeniowe (cele obliczeniowe) do obszaru roboczego, aby używać go do uczenia maszynowego i wnioskowania
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc4256ae0591e9fc82dcdce7c66514710fad3f57
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711378"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Skonfiguruj cele obliczeniowe dla szkolenia i wdrażania modelu

Dowiedz się, jak dołączać zasoby obliczeniowe platformy Azure do obszaru roboczego Azure Machine Learning.  Następnie możesz użyć tych zasobów jako [elementów docelowych](concept-compute-target.md) szkoleniowych i wnioskowania w zadaniach uczenia maszynowego.

W tym artykule dowiesz się, jak skonfigurować obszar roboczy do korzystania z tych zasobów obliczeniowych:

* Komputer lokalny
* Zdalne maszyny wirtualne
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Wystąpienie kontenera platformy Azure

Aby używać obiektów docelowych obliczeń zarządzanych przez Azure Machine Learning, zobacz:


* [Wystąpienie obliczeniowe usługi Azure Machine Learning](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning klaster obliczeniowy](how-to-create-attach-compute-cluster.md)
* [Klaster usługi Azure Kubernetes](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Ograniczenia

* **Nie należy tworzyć wielu jednoczesnych załączników do tego samego obliczenia** w obszarze roboczym. Można na przykład dołączyć jeden klaster usługi Azure Kubernetes do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

    Jeśli chcesz ponownie dołączyć obiekt docelowy obliczeń, na przykład aby zmienić ustawienia konfiguracji TLS lub innego klastra, musisz najpierw usunąć istniejący załącznik.

## <a name="whats-a-compute-target"></a>Co to jest obiekt docelowy obliczeń?

Dzięki Azure Machine Learning można nauczyć model na różnych zasobach lub środowiskach, zbiorczo nazywanymi [__obiektami docelowymi obliczeń__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.  Elementy docelowe obliczeń są również używane do wdrażania modelu, zgodnie z opisem w artykule ["gdzie i jak wdrażać modele"](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Komputer lokalny

W przypadku korzystania z komputera lokalnego do **szkolenia**nie ma potrzeby tworzenia obiektu docelowego obliczeń.  Po prostu [Prześlij przebieg szkolenia](how-to-set-up-training-targets.md) z komputera lokalnego.

W przypadku korzystania z komputera lokalnego do **wnioskowania**należy zainstalować platformę Docker. Aby przeprowadzić wdrożenie, należy użyć [LocalWebservice.deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) do zdefiniowania portu, który będzie używany przez usługę sieci Web. Następnie użyj normalnego procesu wdrażania, zgodnie z opisem w artykule [Wdrażanie modeli za pomocą Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Zdalne maszyny wirtualne

Azure Machine Learning obsługuje również pobieranie własnego zasobu obliczeniowego i dołączanie go do obszaru roboczego. Jeden taki typ zasobu jest dowolną zdalną maszyną wirtualną, o ile jest dostępny z Azure Machine Learning. Zasób może być maszyną wirtualną platformy Azure, serwerem zdalnym w organizacji lub lokalnym. W odniesieniu do adresów IP i poświadczeń (nazwy użytkownika i hasła lub klucza SSH) można użyć dowolnej dostępnej maszyny wirtualnej do zdalnego uruchomienia.

Można użyć wbudowanego w systemie środowiska Conda, już istniejącego środowiska Python lub kontenera Docker. Aby można było wykonać operację na kontenerze platformy Docker, na maszynie wirtualnej musi być uruchomiony aparat platformy Docker. Ta funkcja jest szczególnie przydatna w przypadku bardziej elastycznego, opartego na chmurze środowiska deweloperskiego/eksperymentowania niż na komputerze lokalnym.

W tym scenariuszu Użyj usługi Azure Data Science Virtual Machine (DSVM) jako maszyny wirtualnej platformy Azure. Ta maszyna wirtualna jest wstępnie skonfigurowanym środowiskiem programistycznym do analizy danych i AI na platformie Azure. Maszyna wirtualna oferuje świadome wybór narzędzi i struktur na potrzeby tworzenia uczenia maszynowego w pełnym cyklu życia. Aby uzyskać więcej informacji na temat używania DSVM z Azure Machine Learning, zobacz [Konfigurowanie środowiska deweloperskiego](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Utwórz**: Utwórz DSVM przed użyciem go do uczenia modelu. Aby utworzyć ten zasób, zapoznaj [się z tematem obsługa Data Science Virtual Machine dla systemu Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem **Ubuntu**. Podczas tworzenia maszyny wirtualnej lub wybrania istniejącej maszyny wirtualnej należy wybrać maszynę wirtualną, która używa Ubuntu.
    > 
    > Azure Machine Learning wymaga również, aby maszyna wirtualna miała __publiczny adres IP__.

1. **Dołącz**: Aby dołączyć istniejącą maszynę wirtualną jako element docelowy obliczeń, należy podać identyfikator zasobu, nazwę użytkownika i hasło dla maszyny wirtualnej. Identyfikator zasobu maszyny wirtualnej można utworzyć przy użyciu identyfikatora subskrypcji, nazwy grupy zasobów i nazwy maszyny wirtualnej przy użyciu następującego formatu ciągu: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Możesz też dołączyć DSVM do obszaru roboczego [za pomocą programu Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Nie należy tworzyć wielu jednoczesnych załączników do tego samego DSVM z obszaru roboczego. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń DSVM. Platformy Docker i Conda są używane do tworzenia i konfigurowania środowiska szkoleniowego na DSVM.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Usługa Azure HDInsight to popularna platforma do analizy danych Big Data. Platforma zapewnia Apache Spark, która może służyć do uczenia modelu.

1. **Utwórz**: Utwórz klaster usługi HDInsight przed użyciem go do uczenia modelu. Aby utworzyć klaster platformy Spark w usłudze HDInsight, zobacz [Tworzenie klastra Spark w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning wymaga, aby klaster usługi HDInsight miał __publiczny adres IP__.

    Podczas tworzenia klastra należy określić nazwę użytkownika i hasło SSH. Zwróć uwagę na te wartości, ponieważ są one potrzebne do korzystania z usługi HDInsight jako elementu docelowego obliczeń.
    
    Po utworzeniu klastra Połącz się z nim za pomocą nazwy hosta \<clustername> -SSH.azurehdinsight.NET, gdzie \<clustername> jest nazwą dostarczoną dla klastra. 

1. **Dołącz**: Aby dołączyć klaster usługi HDInsight jako element docelowy obliczeń, należy podać identyfikator zasobu, nazwę użytkownika i hasło dla klastra usługi HDInsight. Identyfikator zasobu klastra usługi HDInsight można utworzyć przy użyciu identyfikatora subskrypcji, nazwy grupy zasobów i nazwy klastra usługi HDInsight przy użyciu następującego formatu ciągu: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Możesz też dołączyć klaster usługi HDInsight do obszaru roboczego [za pomocą programu Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Nie należy tworzyć wielu jednoczesnych załączników do tej samej usługi HDInsight z obszaru roboczego. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch służy do wydajnego uruchamiania aplikacji równoległych i o wysokiej wydajności obliczeniowych (HPC) w chmurze. AzureBatchStep można użyć w potoku Azure Machine Learning do przesyłania zadań do Azure Batch puli maszyn.

Aby dołączyć Azure Batch jako obiekt docelowy obliczeń, należy użyć zestawu SDK Azure Machine Learning i podać następujące informacje:

-    **Azure Batch nazwa obliczenia**: przyjazna nazwa do użycia dla obliczeń w obszarze roboczym
-    **Nazwa konta Azure Batch**: nazwa konta Azure Batch
-    **Grupa zasobów**: Grupa zasobów, która zawiera konto Azure Batch.

Poniższy kod ilustruje sposób dołączania Azure Batch jako obiekt docelowy obliczeń:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego Azure Batch z obszaru roboczego. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks jest środowiskiem opartym na Apache Spark w chmurze platformy Azure. Może służyć jako obiekt docelowy obliczeń z potokiem Azure Machine Learning.

Utwórz obszar roboczy Azure Databricks, zanim go użyjesz. Aby utworzyć zasób obszaru roboczego, zobacz dokument [Uruchamianie zadania Spark na Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Aby dołączyć Azure Databricks jako element docelowy obliczeń, podaj następujące informacje:

* __Nazwa obliczeniowa datakostek__: nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Nazwa obszaru roboczego elementów datakostki__: Nazwa obszaru roboczego Azure Databricks.
* __Token dostępu do datakostki__: token dostępu używany do uwierzytelniania w Azure Databricks. Aby wygenerować token dostępu, zobacz dokument [uwierzytelniania](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) .

Poniższy kod ilustruje sposób dołączania Azure Databricks jako obiektu docelowego obliczeń przy użyciu zestawu SDK Azure Machine Learning (__obszar roboczy datakostki musi znajdować się w tej samej subskrypcji co obszar roboczy AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Aby zapoznać się z bardziej szczegółowym przykładem, zobacz [przykładowy Notes](https://aka.ms/pl-databricks) w witrynie GitHub.

> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego Azure Databricks z obszaru roboczego. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics to platforma analizy danych Big Data w chmurze platformy Azure. Może służyć jako obiekt docelowy obliczeń z potokiem Azure Machine Learning.

Utwórz konto Azure Data Lake Analytics przed użyciem go. Aby utworzyć ten zasób, zapoznaj się z dokumentem [wprowadzenie do Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

Aby dołączyć Data Lake Analytics jako obiekt docelowy obliczeń, należy użyć zestawu SDK Azure Machine Learning i podać następujące informacje:

* __Nazwa obliczania__: nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Grupa zasobów__: Grupa zasobów, która zawiera konto Data Lake Analytics.
* __Nazwa konta__: nazwa konta Data Lake Analytics.

Poniższy kod ilustruje sposób dołączania Data Lake Analytics jako obiekt docelowy obliczeń:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Aby zapoznać się z bardziej szczegółowym przykładem, zobacz [przykładowy Notes](https://aka.ms/pl-adla) w witrynie GitHub.

> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego ADLA z obszaru roboczego. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

> [!TIP]
> Potoki Azure Machine Learning mogą korzystać tylko z danych przechowywanych w domyślnym magazynie danych konta Data Lake Analytics. Jeśli dane, które mają być używane, należą do magazynu innego niż domyślny, można użyć programu [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) do skopiowania danych przed szkoleniem.

## <a name="azure-container-instance"></a><a id="aci"></a>Wystąpienie kontenera platformy Azure

Azure Container Instances (ACI) są tworzone dynamicznie podczas wdrażania modelu. Nie można utworzyć ani dołączyć ACI do obszaru roboczego w żaden inny sposób. Aby uzyskać więcej informacji, zobacz [Wdrażanie modelu do Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) umożliwia korzystanie z różnych opcji konfiguracji w przypadku korzystania z Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [jak utworzyć i dołączyć usługę Azure Kubernetes](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy, aby poznać przykłady szkoleń z różnymi obiektami docelowymi obliczeń:
* [Jak używać — Azure/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Użyj zasobu obliczeniowego, aby [skonfigurować i przesłać przebieg szkoleniowy](how-to-set-up-training-targets.md).
* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](how-to-enable-virtual-network.md)
