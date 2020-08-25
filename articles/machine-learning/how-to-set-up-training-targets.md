---
title: Użyj obiektów docelowych obliczeń do szkolenia modelu
titleSuffix: Azure Machine Learning
description: Skonfiguruj środowiska szkoleniowe (cele obliczeniowe) dla szkolenia modelu uczenia maszynowego. Można łatwo przełączać się między środowiskami szkoleniowymi. Zacznij szkolenie lokalnie. Jeśli konieczne jest skalowanie w poziomie, przełącz się do elementu docelowego obliczeń opartego na chmurze.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ced05e8ccd04775df189e9dff1af1fdaa990f3b2
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751670"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Skonfiguruj cele obliczeń i używaj ich do szkolenia modelu 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dzięki Azure Machine Learning można nauczyć model na różnych zasobach lub środowiskach, zbiorczo nazywanymi [__obiektami docelowymi obliczeń__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.  Możesz również utworzyć cele obliczeniowe dla wdrożenia modelu, zgodnie z opisem w artykule ["gdzie i jak wdrażać modele"](how-to-deploy-and-where.md).

Można utworzyć obiekt docelowy obliczeń i zarządzać nim przy użyciu rozszerzenia Azure Machine Learning SDK, Azure Machine Learning Studio, interfejsu wiersza polecenia platformy Azure lub Azure Machine Learning VS Code. Jeśli masz cele obliczeniowe, które zostały utworzone za pomocą innej usługi (na przykład klastra HDInsight), możesz ich użyć, dołączając je do obszaru roboczego Azure Machine Learning.
 
W tym artykule dowiesz się, jak używać różnych obiektów docelowych obliczeń do uczenia modelu.  Kroki dla wszystkich obiektów docelowych obliczeń są zgodne z tym samym przepływem pracy:
1. __Utwórz__ obiekt docelowy obliczeń, jeśli jeszcze go nie masz.
2. __Dołącz__ obiekt docelowy obliczeń do obszaru roboczego.
3. __Skonfiguruj__ cel obliczeń tak, aby zawierał środowisko Python i zależności pakietów, które są niezbędne przez skrypt.


>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK Azure Machine Learning 1.0.74.

## <a name="compute-targets-for-training"></a>Cele obliczeniowe dla szkolenia

Azure Machine Learning ma różne wsparcie dla różnych obiektów docelowych obliczeń. Typowy cykl projektowania modelu rozpoczyna się od tworzenia i eksperymentowania w niewielkiej ilości danych. Na tym etapie zalecamy używanie środowiska lokalnego. Na przykład na komputerze lokalnym lub maszynie wirtualnej opartej na chmurze. Podczas skalowania w górę szkolenia w przypadku większych zestawów danych lub przeprowadzenia szkolenia rozproszonego zalecamy użycie funkcji Azure Machine Learning COMPUTE do utworzenia wielowęzłowego klastra, który automatycznie skaluje się przy każdym przesyłaniu przebiegu. Możesz również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić w następujący sposób:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning klastrów obliczeniowych można utworzyć jako zasób trwały lub utworzyć dynamicznie w przypadku żądania uruchomienia. Tworzenie na podstawie uruchomienia powoduje usunięcie obiektu docelowego obliczeń po zakończeniu szkolenia, dlatego nie można ponownie użyć obiektów docelowych obliczeń utworzonych w ten sposób.

## <a name="whats-a-run-configuration"></a>Co to jest Konfiguracja przebiegu?

Podczas szkolenia często rozpoczyna się na komputerze lokalnym, a następnie uruchamia ten skrypt szkoleniowy na innym miejscu docelowym obliczeń. Za pomocą Azure Machine Learning można uruchomić skrypt na różnych obiektach docelowych obliczeń bez konieczności zmiany skryptu.

Wystarczy zdefiniować środowisko dla każdego obiektu docelowego obliczeń w ramach **konfiguracji przebiegu**.  Następnie, gdy chcesz uruchomić eksperyment szkoleniowy w innym miejscu docelowym obliczeń, określ konfigurację uruchamiania dla tego obliczenia. Aby uzyskać szczegółowe informacje dotyczące określania środowiska i powiązania go z konfiguracją, zobacz [Tworzenie środowisk i zarządzanie nimi w celu szkolenia i wdrażania](how-to-use-environments.md).

Dowiedz się więcej o [przesyłaniu eksperymentów](#submit) na końcu tego artykułu.

## <a name="whats-an-estimator"></a>Co to jest szacowania?

Aby ułatwić uczenie modeli przy użyciu popularnych platform, zestaw SDK języka Python, Azure Machine Learning, zawiera alternatywne abstrakcyjne streszczenie, klasy szacowania.  Ta klasa umożliwia łatwe konstruowanie konfiguracji uruchomieniowych. Można utworzyć i użyć generycznej [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptów szkoleniowych, które korzystają z wybranej platformy szkoleniowej (na przykład scikit-Learning). Zalecamy użycie szacowania do szkolenia, ponieważ automatycznie konstruuje obiekty osadzone, takie jak środowisko lub obiekty RunConfiguration. Jeśli chcesz mieć większą kontrolę nad sposobem tworzenia tych obiektów i określać pakiety do zainstalowania w ramach eksperymentu, wykonaj [następujące kroki](#amlcompute) , aby przesłać eksperymenty szkoleniowe przy użyciu obiektu RunConfiguration w ramach obliczeń Azure Machine Learning.

Azure Machine Learning udostępnia specyficzne szacowania dla [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [łańcucher](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)i [Ray RLlib](how-to-use-reinforcement-learning.md).

Aby uzyskać więcej informacji, zobacz [uczenie modeli ml z szacowania](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Co to jest potok ML?

Potoki ML umożliwiają optymalizację przepływu pracy dzięki prostoty, szybkości, przenośności i ponownego użycia. Podczas kompilowania potoków z Azure Machine Learning możesz skupić się na wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji.

Potoki ML są zbudowane z wielu **kroków**, które są odrębnymi jednostkami obliczeniowymi w potoku. Każdy krok można uruchamiać niezależnie i używać izolowanych zasobów obliczeniowych. Takie podejście umożliwia wielu analitykom danych jednoczesne działanie w tym samym potoku bez zasobów obliczeniowych, a także ułatwia korzystanie z różnych typów i rozmiarów obliczeniowych dla każdego kroku.

> [!TIP]
> Potoki ML mogą korzystać z konfiguracji uruchamiania lub szacowania podczas uczenia modeli.

Chociaż potoki ML mogą szkolić modele, mogą również przygotowywać dane przed szkoleniem i wdrażać modele po szkoleniu. Jednym z głównych przypadków użycia potoków jest ocenianie wsadowe. Aby uzyskać więcej informacji, zobacz [potoki: Optymalizowanie przepływów pracy uczenia maszynowego](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Konfiguracja w języku Python

Skorzystaj z poniższych sekcji, aby skonfigurować te elementy docelowe obliczeń:

* [Komputer lokalny](#local)
* [Azure Machine Learning klaster obliczeniowy](#amlcompute)
* [Wystąpienie obliczeniowe usługi Azure Machine Learning](#instance)
* [Zdalne maszyny wirtualne](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Komputer lokalny

1. **Utwórz i Dołącz**: nie ma potrzeby tworzenia lub dołączania obiektu docelowego obliczeń, aby użyć komputera lokalnego jako środowiska szkoleniowego.  

1. **Konfiguracja**: w przypadku używania komputera lokalnego jako obiektu docelowego obliczeń kod szkoleniowy jest uruchamiany w [środowisku deweloperskim](how-to-configure-environment.md).  Jeśli to środowisko ma już potrzebne pakiety języka Python, użyj środowiska zarządzanego przez użytkownika.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).

### <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning klaster obliczeniowy

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

    Można też utworzyć i dołączyć trwały zasób obliczeniowy Azure Machine Learning w programie [Azure Machine Learning Studio](#portal-create).

   
1. **Konfiguracja**: Utwórz konfigurację uruchamiania dla trwałego elementu docelowego obliczeń.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).

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

 Azure Machine Learning Klastry obliczeniowe obsługują również [zarządzane tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania dostępu do zasobów platformy Azure bez uwzględniania poświadczeń w kodzie. Istnieją dwa typy tożsamości zarządzanych:

* **Tożsamość zarządzana przypisana przez system** jest włączona bezpośrednio w klastrze obliczeniowym Azure Machine Learning. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z klastrem obliczeniowym. Jeśli klaster obliczeniowy zostanie usunięty, platforma Azure automatycznie czyści poświadczenia i tożsamość w usłudze Azure AD.
* **Tożsamość zarządzana przypisana przez użytkownika** to autonomiczny zasób platformy Azure dostarczany za pomocą usługi tożsamości zarządzanej platformy Azure. Tożsamość zarządzaną przez użytkownika można przypisać do wielu zasobów i będzie trwała tak długo, jak chcesz.

Aby określić zarządzaną tożsamość klastra obliczeniowego, użyj dowolnego z tych metod:
    
* W programie Studio podczas tworzenia klastra obliczeniowego lub podczas edytowania szczegółów klastra obliczeniowego Przełącz opcję **Przypisz tożsamość zarządzaną** i określ tożsamość przypisaną przez użytkownika lub tożsamość.
    
* Za pomocą zestawu SDK języka Python Ustaw `identity_type` atrybut w konfiguracji aprowizacji.  
    
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )

    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

* Za pomocą zestawu SDK języka Python Ustaw `identity_type` `identity_id` atrybuty i (Jeśli tworzysz tożsamość zarządzaną przypisane przez użytkownika) w konfiguracji aprowizacji.  
    
    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")

    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```
    
* Korzystając z interfejsu wiersza polecenia, należy ustawić `assign-identity` atrybut podczas tworzenia klastra:
    
    ```azurecli
    # create a cluster with a user-assigned managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # create a cluster with a system-managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'

* Using the CLI, execute the following commands to assign a managed identity on an existing cluster:
    
    ```azurecli
    # add a user-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # add a system-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'

> [!NOTE]
> Azure Machine Learning compute clusters support only **one system-assigned identity** or **multiple user-assigned identities**, not both concurrently.
> 
> Additionally, you can assign only one managed identity from the studio.

#### Managed identity usage

AML defines the **default managed identity** as the system-assigned managed identity or the first user-assigned managed identity.

During a run there are two applications of an identity:
1. The system uses an identity to setup the user's storage mounts, container registry, and datastores.
    * In this case, the system will use the default managed identity.

1. The user applies an identity to access resources from within the code for a submitted run
    
    * In this case, the user must provide the *client_id* corresponding to the managed identity they want to use to retrieve a credential. 
    * Alternatively, AML exposes the user-assigned identity's client id through the *DEFAULT_IDENTITY_CLIENT_ID* environment variable.
    
    For example, to retrieve a token for a datastore with the default managed identity:
    
    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')



### <a id="instance"></a>Azure Machine Learning compute instance

[Azure Machine Learning compute instance](concept-compute-instance.md) is a managed-compute infrastructure that allows you to easily create a single VM. The compute is created within your workspace region, but unlike a compute cluster, an instance cannot be shared with other users in your workspace. Also the instance does not automatically scale down.  You must stop the resource to prevent ongoing charges.

A compute instance can run multiple jobs in parallel and has a job queue. 

Compute instances can run jobs securely in a [virtual network environment](how-to-enable-virtual-network.md#compute-instance), without requiring enterprises to open up SSH ports. The job executes in a containerized environment and packages your model dependencies in a Docker container. 

1. **Create and attach**: 
    
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

1. **Konfiguracja**: Tworzenie konfiguracji uruchomieniowej.
    
    ```python
    
    from azureml.core import ScriptRunConfig
    from azureml.core.runconfig import DEFAULT_CPU_IMAGE
    
    src = ScriptRunConfig(source_directory='', script='train.py')
    
    # Set compute target to the one created in previous step
    src.run_config.target = instance
    
    # Set environment
    src.run_config.environment = myenv
     
    run = experiment.submit(config=src)
    ```

Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie przebiegu szkoleniowego](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Zdalne maszyny wirtualne

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

   Możesz też dołączyć DSVM do obszaru roboczego [za pomocą programu Azure Machine Learning Studio](#portal-reuse).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń DSVM. Platformy Docker i Conda są używane do tworzenia i konfigurowania środowiska szkoleniowego na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

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

   Możesz też dołączyć klaster usługi HDInsight do obszaru roboczego [za pomocą programu Azure Machine Learning Studio](#portal-reuse).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Usługa Azure Batch 

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Konfiguracja w programie Azure Machine Learning Studio

Możesz uzyskać dostęp do obiektów docelowych obliczeń skojarzonych z obszarem roboczym w programie Azure Machine Learning Studio.  Możesz użyć programu Studio do:

* [Wyświetl cele obliczeń](#portal-view) dołączone do obszaru roboczego
* [Tworzenie obiektu docelowego obliczeń](#portal-create) w obszarze roboczym
* [Dołącz obiekt docelowy obliczeń](#portal-reuse) , który został utworzony poza obszarem roboczym


Po utworzeniu elementu docelowego i dołączeniu go do obszaru roboczego będzie on używany w konfiguracji przebiegu z `ComputeTarget` obiektem: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Wyświetl cele obliczeń


Aby zobaczyć cele obliczeń dla obszaru roboczego, wykonaj następujące czynności:

1. Przejdź do [Azure Machine Learning Studio](https://ml.azure.com).
 
1. W obszarze __aplikacje__wybierz pozycję __obliczenia__.

    [![Wyświetl kartę obliczenia](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Tworzenie obiektu docelowego obliczeń

Wykonaj poprzednie kroki, aby wyświetlić listę elementów docelowych obliczeń. Następnie wykonaj następujące kroki, aby utworzyć obiekt docelowy obliczeń: 

1. Wybierz znak plus (+), aby dodać obiekt docelowy obliczeń.

    ![Dodaj element docelowy obliczeń](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Wprowadź nazwę dla elementu docelowego obliczeń. 

1. Wybierz **środowisko obliczeniowe usługi Machine Learning** jako typ obliczeń do użycia na potrzeby __szkolenia__. 

    >[!NOTE]
    >Azure Machine Learning COMPUTE to jedyny zasób obliczeniowy, który można utworzyć w programie Azure Machine Learning Studio.  Wszystkie inne zasoby obliczeniowe mogą być dołączane po ich utworzeniu.

1. Wypełnij formularz. Podaj wartości dla wymaganych właściwości, szczególnie **rodziny maszyn wirtualnych**i **maksymalną liczbę węzłów** do użycia w celu uruchomienia obliczeń.  

1. Wybierz przycisk __Utwórz__.


1. Wyświetl stan operacji tworzenia, wybierając obiekt docelowy obliczeń z listy:

    ![Wybierz element docelowy obliczeń, aby wyświetlić stan operacji tworzenia](./media/how-to-set-up-training-targets/View_list.png)

1. Zobaczysz szczegóły dotyczące obiektu docelowego obliczeń: 

    ![Wyświetl szczegóły komputera docelowego](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Dołącz cele obliczeń

Aby używać obiektów docelowych obliczeń utworzonych poza obszarem roboczym Azure Machine Learning, należy je dołączyć. Dołączenie obiektu docelowego obliczeń sprawia, że jest on dostępny dla Twojego obszaru roboczego.

Wykonaj kroki opisane wcześniej, aby wyświetlić listę elementów docelowych obliczeń. Następnie wykonaj następujące kroki, aby dołączyć obiekt docelowy obliczeń: 

1. Wybierz znak plus (+), aby dodać obiekt docelowy obliczeń. 
1. Wprowadź nazwę dla elementu docelowego obliczeń. 
1. Wybierz typ obliczeń do dołączenia do __szkolenia__:

    > [!IMPORTANT]
    > Nie wszystkie typy obliczeniowe mogą być dołączane z programu Azure Machine Learning Studio. Typy obliczeń, które są obecnie dołączone do szkolenia, obejmują:
    >
    > * Zdalna maszyna wirtualna
    > * Azure Databricks (do użycia w potokach uczenia maszynowego)
    > * Azure Data Lake Analytics (do użycia w potokach uczenia maszynowego)
    > * Azure HDInsight

1. Wypełnij formularz i podaj wartości wymaganych właściwości.

    > [!NOTE]
    > Firma Microsoft zaleca korzystanie z kluczy SSH, które są bezpieczniejsze niż hasła. Hasła są podatne na ataki z wymuszeniem. Klucze SSH korzystają z podpisów kryptograficznych. Aby uzyskać informacje na temat sposobu tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines, zobacz następujące dokumenty:
    >
    > * [Tworzenie i Używanie kluczy SSH w systemie Linux lub macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i Używanie kluczy SSH w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wybierz pozycję __Dołącz__. 
1. Wyświetl stan operacji dołączania, wybierając obiekt docelowy obliczeń z listy.

## <a name="set-up-with-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia

Możesz uzyskać dostęp do obiektów docelowych obliczeń skojarzonych z obszarem roboczym przy użyciu [rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) dla Azure Machine Learning.  Interfejsu wiersza polecenia można użyć do:

* Tworzenie zarządzanego obiektu docelowego obliczeń
* Aktualizowanie zarządzanego obiektu docelowego obliczeń
* Dołącz niezarządzany cel obliczeń

Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Konfiguracja przy użyciu VS Code

Możesz uzyskiwać dostęp do obiektów docelowych obliczeń skojarzonych z obszarem roboczym, tworzyć je i zarządzać nimi, korzystając z [rozszerzenia VS Code](how-to-manage-resources-vscode.md#compute-clusters) Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Prześlij uruchomienie szkolenia przy użyciu zestawu SDK Azure Machine Learning

Po utworzeniu konfiguracji przebiegu należy użyć jej do uruchomienia eksperymentu.  Wzorzec kodu do przesyłania przebiegu szkoleniowego jest taki sam dla wszystkich typów obiektów docelowych obliczeń:

1. Utwórz eksperyment do uruchomienia
1. Prześlij przebieg.
1. Poczekaj na ukończenie przebiegu.

> [!IMPORTANT]
> Po przesłaniu przebiegu szkoleniowego tworzona jest migawka katalogu zawierającego skrypty szkoleniowe i wysyłane do obiektu docelowego obliczeń. Jest on również przechowywany w ramach eksperymentu w obszarze roboczym. Jeśli zmienisz pliki i prześlesz ponownie uruchomienie, zostaną przekazane tylko zmienione pliki.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Aby uzyskać więcej informacji, zobacz [migawki](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Najpierw utwórz eksperyment w obszarze roboczym.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

Prześlij eksperyment z `ScriptRunConfig` obiektem.  Ten obiekt obejmuje:

* **source_directory**: Katalog źródłowy zawierający skrypt szkoleniowy
* **skrypt**: Identyfikowanie skryptu szkoleniowego
* **run_config**: Konfiguracja uruchamiania, która z kolei definiuje, gdzie nastąpi szkolenie.

Na przykład, aby użyć konfiguracji [lokalnej lokalizacji docelowej](#local) :

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Przełączenie tego samego eksperymentu w celu uruchomienia go w innym miejscu docelowym obliczeń przy użyciu innej konfiguracji uruchomieniowej, takiej jak [amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Ten przykład domyślnie używa tylko jednego węzła elementu docelowego obliczeń do szkolenia. Aby użyć więcej niż jednego węzła, ustaw `node_count` konfigurację uruchamiania na żądaną liczbę węzłów. Na przykład poniższy kod ustawia liczbę węzłów używanych do szkolenia w czterech:
>
> ```python
> src.run_config.node_count = 4
> ```

Możesz też:

* Prześlij eksperyment z `Estimator` obiektem, jak pokazano w [pouczeniu modeli ml z szacowania](how-to-train-ml-models.md).
* Prześlij przebieg dla [strojenia parametru](how-to-tune-hyperparameters.md).
* Prześlij eksperyment za pośrednictwem [rozszerzenia vs Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Aby uzyskać więcej informacji, zobacz dokumentację [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) i [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) .

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Utwórz konfigurację uruchamiania i prześlij przebieg przy użyciu interfejsu wiersza polecenia Azure Machine Learning

Możesz użyć [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i [Machine Learning rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) , aby utworzyć konfigurację uruchamiania i przesłać przebiegi w różnych obiektach docelowych obliczeń. W poniższych przykładach założono, że masz istniejące Obszar roboczy usługi Azure Machine Learning i zalogujesz się do platformy Azure przy użyciu `az login` polecenia interfejsu wiersza poleceń. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Utwórz konfigurację uruchamiania

Najprostszym sposobem tworzenia konfiguracji uruchamiania jest nawigowanie po folderze zawierającym skrypty środowiska Python uczenia maszynowego i używanie interfejsu wiersza polecenia

```azurecli
az ml folder attach
```

To polecenie tworzy podfolder `.azureml` zawierający pliki konfiguracji uruchamiania dla różnych elementów docelowych obliczeń. Można kopiować i edytować te pliki, aby dostosować konfigurację, na przykład dodać pakiety Python lub zmienić ustawienia platformy Docker.  

### <a name="structure-of-run-configuration-file"></a>Struktura pliku konfiguracji uruchamiania

Plik konfiguracji uruchomieniowej jest YAML sformatowany z następującymi sekcjami
 * Skrypt do uruchomienia i jego argumentów
 * Nazwa elementu docelowego obliczeń, wartość "Local" lub nazwa obliczeń w obszarze roboczym.
 * Parametry do wykonywania przebiegu: Framework, programu Communicator dla rozproszonych przebiegów, maksymalny czas trwania i liczba węzłów obliczeniowych.
 * Sekcja środowiska. Szczegóły dotyczące pól w tej sekcji znajdują się w temacie [Tworzenie środowisk szkoleniowych i zarządzanie nimi](how-to-use-environments.md) .
   * Aby określić pakiety języka Python, które mają zostać zainstalowane dla programu Run, Utwórz [plik środowiska Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)i ustaw wartość pola __condaDependenciesFile__ .
 * Wykonaj szczegóły historii, aby określić folder pliku dziennika oraz włączyć lub wyłączyć migawki wyjściowe kolekcji i uruchamiania historii.
 * Szczegóły konfiguracji specyficzne dla wybranego środowiska.
 * Odwołania do danych i szczegóły magazynu danych.
 * Szczegóły konfiguracji specyficzne dla środowisko obliczeniowe usługi Machine Learning tworzenia nowego klastra.

Zobacz przykładowy [plik JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) dla całego schematu runconfig.

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Najpierw utwórz eksperyment dla przebiegów

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Uruchomienie skryptu

Aby przesłać uruchomienie skryptu, wykonaj polecenie

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Przebieg z dysku

Do przeprowadzenia przebiegu strojenia parametrów można użyć polecenia popełnienia z interfejsem CLI platformy Azure. Najpierw utwórz plik konfiguracji z dysku w następującym formacie. Aby uzyskać szczegółowe informacje na temat parametrów strojenia parametrów, zobacz [dostrajanie parametrów w](how-to-tune-hyperparameters.md) artykule dotyczącym modelu.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Dodaj ten plik obok plików konfiguracji uruchomieniowej. Następnie prześlij dysk Uruchom za pomocą polecenia:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Zwróć uwagę na sekcję *argumenty* w runconfig i *przestrzeni parametrów* w pliku config. Zawierają one argumenty wiersza polecenia, które mają być przekazane do skryptu szkoleniowego. Wartość w runconfig pozostaje taka sama dla każdej iteracji, podczas gdy zakres w konfiguracji dysku jest powtarzany. Nie określaj tego samego argumentu w obu plikach.

Aby uzyskać więcej informacji na temat tych ```az ml``` poleceń interfejsu wiersza polecenia, zobacz [dokumentację referencyjną](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy, aby poznać przykłady szkoleń z różnymi obiektami docelowymi obliczeń:
* [Jak używać — Azure/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl odwołanie do zestawu SDK [klasy RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) .
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](how-to-enable-virtual-network.md)
