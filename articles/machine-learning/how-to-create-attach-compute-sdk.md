---
title: Tworzenie zasobów obliczeniowych za pomocą zestawu SDK języka Python
titleSuffix: Azure Machine Learning
description: Użyj zestawu SDK języka Python Azure Machine Learning, aby utworzyć zasoby obliczeniowe szkolenia i wdrożenia (cele obliczeniowe) dla uczenia maszynowego
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 96aa6839fe51bb8a8c26f411c1a1f9df6b8c5a7f
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147499"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Utwórz cele obliczeniowe dla szkolenia i wdrażania modelu w języku Python SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule Użyj zestawu SDK języka Python Azure Machine Learning, aby utworzyć cele obliczeń i zarządzać nimi. Można również tworzyć cele obliczeń i zarządzać nimi za pomocą:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md), 
* [Rozszerzenie interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md#resource-management) dla Azure Machine Learning
* [Vs Code rozszerzenie](how-to-manage-resources-vscode.md#compute-clusters) Azure Machine Learning.


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj
* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Co to jest obiekt docelowy obliczeń?

Dzięki Azure Machine Learning można nauczyć model na różnych zasobach lub środowiskach, zbiorczo nazywanymi [__obiektami docelowymi obliczeń__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.  Możesz również utworzyć cele obliczeniowe dla wdrożenia modelu, zgodnie z opisem w artykule ["gdzie i jak wdrażać modele"](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Cele obliczeniowe dla szkolenia

Azure Machine Learning ma różne wsparcie dla różnych obiektów docelowych obliczeń. Typowy cykl projektowania modelu rozpoczyna się od tworzenia i eksperymentowania w niewielkiej ilości danych. Na tym etapie zalecamy używanie środowiska lokalnego. Na przykład na komputerze lokalnym lub maszynie wirtualnej opartej na chmurze. Podczas skalowania w górę szkolenia w przypadku większych zestawów danych lub przeprowadzenia szkolenia rozproszonego zalecamy użycie funkcji Azure Machine Learning COMPUTE do utworzenia wielowęzłowego klastra, który automatycznie skaluje się przy każdym przesyłaniu przebiegu. Możesz również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić w następujący sposób:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning klastrów obliczeniowych można utworzyć jako zasób trwały lub utworzyć dynamicznie w przypadku żądania uruchomienia. Tworzenie na podstawie uruchomienia powoduje usunięcie obiektu docelowego obliczeń po zakończeniu szkolenia, dlatego nie można ponownie użyć obiektów docelowych obliczeń utworzonych w ten sposób.

Skorzystaj z poniższych sekcji, aby skonfigurować te elementy docelowe obliczeń:

* [Komputer lokalny](#local)
* [Azure Machine Learning klaster obliczeniowy](#amlcompute)
* [Wystąpienie obliczeniowe usługi Azure Machine Learning](#instance)
* [Zdalne maszyny wirtualne](#vm)
* [Azure HDInsight](#hdinsight)


## <a name="local-computer"></a><a id="local"></a>Komputer lokalny

W przypadku korzystania z komputera lokalnego do szkolenia nie ma potrzeby tworzenia obiektu docelowego obliczeń.  Po prostu [Prześlij przebieg szkolenia](how-to-set-up-training-targets.md) z komputera lokalnego.

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning klaster obliczeniowy

Azure Machine Learning klaster obliczeniowy to infrastruktura obliczeniowa, która umożliwia łatwe tworzenie obliczeń jednego lub wielowęzłowego. Obliczenia są tworzone w regionie obszaru roboczego jako zasób, który może być współużytkowany z innymi użytkownikami w obszarze roboczym. Obliczenia są skalowane automatycznie podczas przesyłania zadania i mogą być umieszczane w Virtual Network platformy Azure. Obliczenia są wykonywane w środowisku kontenerowym i pakiety zależności modelu w [kontenerze platformy Docker](https://www.docker.com/why-docker).

Za pomocą obliczeń Azure Machine Learning można dystrybuować proces uczenia w klastrze procesorów CPU lub węzłów obliczeniowych procesora GPU w chmurze. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, które obejmują procesory GPU, zobacz [rozmiary maszyny wirtualnej zoptymalizowanej według procesora GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Azure Machine Learning COMPUTE ma limity domyślne, takie jak liczba rdzeni, które można przydzielić. Aby uzyskać więcej informacji, zobacz [Zarządzanie przydziałami zasobów platformy Azure i ich żądania](how-to-manage-quotas.md).

> [!TIP]
> Klastry mogą zwykle skalować do 100 węzłów, o ile masz wystarczające limity przydziału dla wymaganej liczby rdzeni. Domyślnie klastry są skonfigurowane z obsługą komunikacji między węzłami między węzłami klastra w celu obsługi zadań MPI na przykład. Można jednak skalować klastry do tysięcy węzłów, po prostu [zwiększając bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)i żądając zezwolenia na wyświetlenie listy subskrypcji lub obszaru roboczego lub określonego klastra w celu wyłączenia komunikacji między węzłami. 

Azure Machine Learning obliczeń można użyć ponownie w ramach przebiegów. Obliczenia mogą być współużytkowane z innymi użytkownikami w obszarze roboczym i zachowywane między działami, automatyczne skalowanie węzłów w górę lub w dół na podstawie liczby przesłanych przebiegów oraz max_nodes ustawionych w klastrze. Ustawienie min_nodes steruje minimalnymi dostępnymi węzłami.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Utwórz i Dołącz**: Aby utworzyć trwały zasób obliczeniowy Azure Machine Learning w języku Python, określ właściwości **vm_size** i **max_nodes** . Azure Machine Learning następnie używa inteligentnych ustawień domyślnych dla innych właściwości. Obliczenia są skalowane automatycznie do zerowych węzłów, gdy nie są używane.   Dedykowane maszyny wirtualne są tworzone w celu uruchamiania zadań zgodnie z potrzebami.
    
    * **vm_size**: rodzina maszyn wirtualnych węzłów utworzonych przez Azure Machine Learning COMPUTE.
    * **max_nodes**: Maksymalna liczba węzłów do automatycznego skalowania w górę do momentu uruchomienia zadania na Azure Machine Learning obliczeń.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Podczas tworzenia Azure Machine Learning obliczeń można także skonfigurować kilka zaawansowanych właściwości. Właściwości umożliwiają tworzenie trwałego klastra o stałym rozmiarze lub w ramach istniejącej Virtual Network platformy Azure w ramach subskrypcji.  Aby uzyskać szczegółowe informacje, zobacz [klasę AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .

    Można też utworzyć i dołączyć trwały zasób obliczeniowy Azure Machine Learning w programie [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#portal-create).

Teraz, po dołączeniu obliczeń, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](how-to-set-up-training-targets.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Obniż koszt klastra obliczeniowego

Możesz również użyć [maszyn wirtualnych o niskim priorytecie](concept-plan-manage-cost.md#low-pri-vm) do uruchamiania niektórych lub wszystkich obciążeń. Te maszyny wirtualne nie mają gwarantowanej dostępności i mogą być przeszukiwane w trakcie użytkowania. Zastępujące zadanie zostało ponownie uruchomione, a nie wznowione. 

Użyj dowolnego z tych metod, aby określić maszynę wirtualną o niskim priorytecie:
    
* W programie Studio wybierz pozycję **niski priorytet** podczas tworzenia maszyny wirtualnej.
    
* Za pomocą zestawu SDK języka Python Ustaw `vm_priority` atrybut w konfiguracji aprowizacji.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Korzystając z interfejsu wiersza polecenia, należy ustawić `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurowanie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Skonfiguruj zarządzaną tożsamość w konfiguracji aprowizacji:  
    
* Tożsamość zarządzana przypisana przez system:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Tożsamość zarządzana przypisana przez użytkownika: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Dodaj zarządzaną tożsamość do istniejącego klastra obliczeniowego:  
    
* Tożsamość zarządzana przypisana przez system:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Tożsamość zarządzana przypisana przez użytkownika:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Użycie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Wystąpienie obliczeniowe usługi Azure Machine Learning

[Wystąpienie obliczeniowe Azure Machine Learning](concept-compute-instance.md) to infrastruktura obliczeń zarządzanych, która umożliwia łatwe tworzenie pojedynczej maszyny wirtualnej. Obliczenia są tworzone w regionie obszaru roboczego, ale w przeciwieństwie do klastra obliczeniowego wystąpienie nie może być współużytkowane z innymi użytkownikami w obszarze roboczym. Również wystąpienie nie jest automatycznie skalowane w dół.  Aby uniknąć bieżących opłat, należy zatrzymać zasób.

Wystąpienie obliczeniowe może uruchamiać wiele zadań równolegle i ma kolejkę zadań. 

Wystąpienia obliczeniowe mogą bezpiecznie uruchamiać zadania w [środowisku sieci wirtualnej](how-to-enable-virtual-network.md#compute-instance), bez konieczności otwierania portów SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku kontenerowym i pakuje zależności modelu w kontenerze platformy Docker. 

1. **Utwórz i Dołącz**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie przebiegu szkoleniowego](how-to-set-up-training-targets.md)


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

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń DSVM. Platformy Docker i Conda są używane do tworzenia i konfigurowania środowiska szkoleniowego na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](how-to-set-up-training-targets.md).

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

> [!TIP]
> Potoki Azure Machine Learning mogą korzystać tylko z danych przechowywanych w domyślnym magazynie danych konta Data Lake Analytics. Jeśli dane, które mają być używane, należą do magazynu innego niż domyślny, można użyć programu [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) do skopiowania danych przed szkoleniem.

## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy, aby poznać przykłady szkoleń z różnymi obiektami docelowymi obliczeń:
* [Jak używać — Azure/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Aby [przesłać przebieg szkoleniowy](how-to-set-up-training-targets.md), użyj zasobu COMPUTE.
* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](how-to-enable-virtual-network.md)
