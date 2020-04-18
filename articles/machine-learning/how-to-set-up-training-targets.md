---
title: Użyj celów obliczeniowych do szkolenia modelu
titleSuffix: Azure Machine Learning
description: Skonfiguruj środowiska szkoleniowe (obiekty docelowe obliczeń) do szkolenia modelu uczenia maszynowego. Możesz łatwo przełączać się między środowiskami treningowymi. Rozpocznij szkolenie lokalnie. Jeśli chcesz skalować w poziomie, przełącz się do obiektu docelowego obliczeń opartych na chmurze.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 625efcce7305cd7b1dd415a286e6b1e92682cc0a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616836"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Konfigurowanie i używanie obiektów docelowych obliczeń do szkolenia modelu 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Za pomocą usługi Azure Machine Learning można szkolić model na różnych zasobach lub środowiskach, łącznie nazywanych [__celami obliczeniowymi.__](concept-azure-machine-learning-architecture.md#compute-targets) Docelowym obiektem obliczeniowym może być maszyna lokalna lub zasób w chmurze, taki jak środowisko obliczeniowe usługi Azure Machine Learning, usługa Azure HDInsight lub zdalna maszyna wirtualna.  Można również utworzyć obiekty docelowe obliczeń dla wdrożenia modelu, jak opisano w ["Gdzie i jak wdrożyć modele"](how-to-deploy-and-where.md).

Obiekt docelowy obliczeń można utworzyć i zarządzać nim za pomocą pakietu SDK usługi Azure Machine Learning, pakietu Azure Machine Learning, interfejsu wiersza polecenia platformy Azure lub usługi Azure Machine Learning VS Code. Jeśli masz obiekty docelowe obliczeń, które zostały utworzone za pośrednictwem innej usługi (na przykład klastra HDInsight), można ich użyć, dołączając je do obszaru roboczego usługi Azure Machine Learning.
 
W tym artykule dowiesz się, jak używać różnych obiektów docelowych obliczeń do szkolenia modelu.  Kroki dla wszystkich obiektów docelowych obliczeń są zgodne z tym samym przepływem pracy:
1. __Utwórz__ cel obliczeniowy, jeśli jeszcze go nie masz.
2. __Dołącz__ cel obliczeniowy do obszaru roboczego.
3. __Skonfiguruj__ obiekt docelowy obliczeń, tak aby zawierał środowisko Języka Python i zależności pakietów wymagane przez skrypt.


>[!NOTE]
> Kod w tym artykule został przetestowany przy testach azure machine learning SDK w wersji 1.0.74.

## <a name="compute-targets-for-training"></a>Obliczaj cele szkoleniowe

Usługa Azure Machine Learning ma różną obsługę w różnych obiektach docelowych obliczeń. Typowy cykl życia rozwoju modelu rozpoczyna się od dev/eksperymentowanie na małej ilości danych. Na tym etapie zaleca się korzystanie ze środowiska lokalnego. Na przykład komputer lokalny lub maszyna wirtualna w chmurze. Podczas skalowania szkolenia na większych zestawów danych lub szkolenia rozproszonego zaleca się użycie usługi Azure Machine Learning Compute do utworzenia klastra jedno- lub wielowęzłowego, który automatycznie skaluje się przy każdym przesłaniu uruchomienia. Można również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić w sposób opisany poniżej:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Obliczenia usługi Azure Machine Learning można utworzyć jako trwały zasób lub utworzone dynamicznie podczas żądania uruchomienia. Tworzenie oparte na przebiegu usuwa obiekt docelowy obliczeń po zakończeniu przebiegu szkolenia, więc nie można ponownie użyć obiektów docelowych obliczeń utworzonych w ten sposób.

## <a name="whats-a-run-configuration"></a>Co to jest konfiguracja uruchamiania?

Podczas szkolenia często można uruchomić na komputerze lokalnym, a później uruchomić ten skrypt szkoleniowy na innym miejscu docelowym obliczeń. Dzięki usłudze Azure Machine Learning można uruchomić skrypt na różnych obiektach docelowych obliczeń bez konieczności zmiany skryptu.

Wszystko, co musisz zrobić, to zdefiniować środowisko dla każdego celu obliczeniowego w **konfiguracji uruchomienia**.  Następnie, jeśli chcesz uruchomić eksperyment szkoleniowy na innym miejscu docelowym obliczeń, określ konfigurację uruchamiania dla tego obliczeń. Aby uzyskać szczegółowe informacje na temat określania środowiska i powiązania go z uruchomieniem konfiguracji, zobacz [Tworzenie środowisk szkoleniowych i wdrożkowych oraz zarządzanie nimi.](how-to-use-environments.md)

Dowiedz się więcej o [przesyłaniu eksperymentów](#submit) na końcu tego artykułu.

## <a name="whats-an-estimator"></a>Co to jest estymator?

Aby ułatwić szkolenie modelu przy użyciu popularnych struktur, zestaw SDK języka Python usługi Azure Machine Learning zawiera alternatywną abstrakcję wyższego poziomu, klasę estymatora.  Ta klasa umożliwia łatwe konstruowanie konfiguracji uruchamiania. Można utworzyć i używać ogólnego [estymatora](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptów szkoleniowych, które używają dowolnej struktury uczenia się wybierzesz (takich jak scikit-learn). Zaleca się użycie estymatora do szkolenia, ponieważ automatycznie konstruuje osadzone obiekty, takie jak środowisko lub RunConfiguration obiektów dla Ciebie. Jeśli chcesz mieć większą kontrolę nad tym, jak te obiekty są tworzone i określić, jakie pakiety do zainstalowania dla eksperymentu uruchomić, wykonaj [następujące kroki,](#amlcompute) aby przesłać eksperymenty szkoleniowe przy użyciu RunConfiguration obiektu na platformie Azure Machine Learning Compute.

W przypadku zadań PyTorch, TensorFlow i Chainer usługa Azure Machine Learning udostępnia również odpowiednie estymatory [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) aby uprościć korzystanie z tych struktur.

Aby uzyskać więcej informacji, zobacz [Train ML Models z estymatorami](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Co to jest potok ML?

Dzięki potokom ml można zoptymalizować przepływ pracy za pomocą prostoty, szybkości, przenośności i ponownego użycia. Podczas tworzenia potoków za pomocą usługi Azure Machine Learning możesz skupić się na swojej wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji.

Potoki ML są zbudowane z wielu **kroków,** które są odrębne jednostki obliczeniowe w potoku. Każdy krok można uruchomić niezależnie i używać izolowanych zasobów obliczeniowych. Dzięki temu wielu analityków danych do pracy na tym samym potoku w tym samym czasie bez nadmiernego opodatkowania zasobów obliczeniowych, a także ułatwia korzystanie z różnych typów obliczeń/rozmiarów dla każdego kroku.

> [!TIP]
> Potoki ML można użyć uruchomienia konfiguracji lub estymatorów podczas modeli szkoleniowych.

Podczas potoków ml można szkolić modele, mogą również przygotować dane przed szkoleniem i wdrażania modeli po szkoleniu. Jednym z podstawowych przypadków użycia dla potoków jest ocenianie partii. Aby uzyskać więcej informacji, zobacz [Potoki: Optymalizowanie przepływów pracy uczenia maszynowego](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Konfiguracja w języku Python

Użyj poniższych sekcji, aby skonfigurować te obiekty docelowe obliczeń:

* [Komputer lokalny](#local)
* [Środowisko obliczeniowe usługi Azure Machine Learning](#amlcompute)
* [Zdalne maszyny wirtualne](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Komputer lokalny

1. **Tworzenie i dołączanie:** Nie ma potrzeby tworzenia ani dołączania obiektu docelowego obliczeń, aby używać komputera lokalnego jako środowiska szkoleniowego.  

1. **Konfigurowanie:** Gdy komputer lokalny jest używany jako obiekt docelowy obliczeń, kod szkolenia jest uruchamiany w [środowisku programistycznym.](how-to-configure-environment.md)  Jeśli to środowisko ma już pakiety języka Python, których potrzebujesz, użyj środowiska zarządzanego przez użytkownika.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Teraz, gdy masz dołączone obliczenia i skonfigurowane uruchomienie, następnym krokiem jest [przesłanie przebiegu szkolenia](#submit).

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Środowisko obliczeniowe usługi Azure Machine Learning

Usługa Azure Machine Learning Compute to infrastruktura obliczeniowa zarządzana, która umożliwia użytkownikowi łatwe tworzenie obliczeń jedno- lub wielowęzłowych. Obliczenia są tworzone w obrębie obszaru roboczego jako zasób, który może być współużytkowane z innymi użytkownikami w obszarze roboczym. Obliczenia skaluje się automatycznie po przesłaniu zadania i mogą być umieszczane w sieci wirtualnej platformy Azure. Obliczenia wykonuje w środowisku konteneryzowanym i pakiety zależności modelu w [kontenerze platformy Docker.](https://www.docker.com/why-docker)

Usługi Azure Machine Learning Compute można używać do dystrybucji procesu szkoleniowego w klastrze węzłów obliczeniowych procesora CPU lub GPU w chmurze. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, które zawierają procesory graficzne, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora graficznego](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Obliczenia usługi Azure Machine Learning mają domyślne limity, takie jak liczba rdzeni, które można przydzielić. Aby uzyskać więcej informacji, zobacz [Zarządzanie przydziałami dla platformy Azure i żądanie ich.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)


Można utworzyć środowisko obliczeniowe usługi Azure Machine Learning na żądanie podczas planowania uruchamiania lub jako trwały zasób.

#### <a name="run-based-creation"></a>Tworzenie oparte na przebiegu

Można utworzyć usługi Azure Machine Learning Compute jako miejsce docelowe obliczeń w czasie wykonywania. Obliczenia są tworzone automatycznie dla twojego uruchomienia. Obliczenia są usuwane automatycznie po zakończeniu uruchamiania. 

> [!IMPORTANT]
> Tworzenie obliczeń usługi Azure Machine Learning oparte na przebiegu na podstawie uruchamiania jest obecnie w wersji zapoznawczej. Nie używaj tworzenia opartego na przebiegu, jeśli używasz automatycznego dostrajania hiperparametrycznego lub automatycznego uczenia maszynowego. Aby użyć dostrajania hiperparametrycznego lub automatycznego uczenia maszynowego, utwórz trwały cel [obliczeniowy.](#persistent)

1.  **Tworzenie, dołączanie i konfigurowanie:** Tworzenie oparte na przebiegu wykonuje wszystkie niezbędne kroki, aby utworzyć, dołączyć i skonfigurować obiekt docelowy obliczeń za pomocą konfiguracji uruchamiania.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Teraz, gdy masz dołączone obliczenia i skonfigurowane uruchomienie, następnym krokiem jest [przesłanie przebiegu szkolenia](#submit).

#### <a name="persistent-compute"></a><a id="persistent"></a>Obliczenia trwałe

Trwałe obliczenia usługi Azure Machine Learning mogą być ponownie za pomocą zadań. Obliczenia mogą być współużytkowane z innymi użytkownikami w obszarze roboczym i jest utrzymywany między zadaniami.

1. **Tworzenie i dołączanie:** Aby utworzyć trwały zasób obliczeniowy usługi Azure Machine Learning w języku Python, określ **właściwości vm_size** i **max_nodes.** Usługa Azure Machine Learning używa inteligentnych ustawień domyślnych dla innych właściwości. Obliczeń skalowania w dół do zera węzłów, gdy nie jest używany.   Dedykowane maszyny wirtualne są tworzone w celu uruchamiania zadań zgodnie z potrzebami.
    
    * **vm_size:** Rodzina maszyn wirtualnych węzłów utworzonych przez usługę Azure Machine Learning Compute.
    * **max_nodes:** Maksymalna liczba węzłów do skalowania automatycznego do po uruchomieniu zadania na platformie Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Podczas tworzenia usługi Azure Machine Learning Compute można również skonfigurować kilka zaawansowanych właściwości. Właściwości umożliwiają utworzenie trwałego klastra o stałym rozmiarze lub w ramach istniejącej sieci wirtualnej platformy Azure w ramach subskrypcji.  Zobacz [AmlCompute klasy,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) aby uzyskać szczegółowe informacje.
    
   Możesz też utworzyć i dołączyć trwały zasób obliczeniowy usługi Azure Machine Learning w [studio usługi Azure Machine Learning.](#portal-create)

1. **Konfigurowanie:** Utwórz konfigurację uruchamiania dla trwałego obiektu docelowego obliczeń.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Teraz, gdy masz dołączone obliczenia i skonfigurowane uruchomienie, następnym krokiem jest [przesłanie przebiegu szkolenia](#submit).


### <a name="remote-virtual-machines"></a><a id="vm"></a>Zdalne maszyny wirtualne

Usługa Azure Machine Learning obsługuje również dostarczanie własnych zasobów obliczeniowych i dołączanie go do obszaru roboczego. Jednym z takich typów zasobów jest dowolnego zdalnego maszyny Wirtualnej, tak długo, jak jest on dostępny z usługi Azure Machine Learning. Zasób może być maszyną wirtualną platformy Azure, serwerem zdalnym w organizacji lub lokalnie. W szczególności, biorąc pod uwagę adres IP i poświadczenia (nazwa użytkownika i hasło lub klucz SSH), można użyć dowolnej dostępnej maszyny Wirtualnej dla zdalnych przebiegów.

Można użyć środowiska conda utworzonego przez system, już istniejącego środowiska Języka Python lub kontenera platformy Docker. Aby wykonać w kontenerze platformy Docker, musi mieć aparat platformy Docker uruchomiony na maszynie wirtualnej. Ta funkcja jest szczególnie przydatna, gdy potrzebujesz bardziej elastycznego środowiska deweloperskiego/eksperymentowego opartego na chmurze niż komputer lokalny.

Użyj maszyny wirtualnej do nauki o danych platformy Azure (DSVM) jako wybranej maszyny wirtualnej platformy Azure w tym scenariuszu. Ta maszyna wirtualna jest wstępnie skonfigurowanym środowiskiem nauki o danych i rozwoju sztucznej inteligencji na platformie Azure. Maszyna wirtualna oferuje wyselekcjonowany wybór narzędzi i struktur do tworzenia uczenia maszynowego w pełnym cyklu życia. Aby uzyskać więcej informacji na temat korzystania z usługi DSVM z usługą Azure Machine Learning, zobacz [Konfigurowanie środowiska programistycznego.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)

1. **Utwórz:** Utwórz dsvm przed użyciem go do szkolenia modelu. Aby utworzyć ten zasób, zobacz [Aprowizuj maszynę wirtualną do nauki o danych dla systemu Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne, które uruchamiają Ubuntu. Podczas tworzenia maszyny Wirtualnej lub wybrać istniejącą maszynę wirtualną, należy wybrać maszynę wirtualną, która używa Ubuntu.

1. **Dołącz:** Aby dołączyć istniejącą maszynę wirtualną jako obiekt docelowy obliczeń, należy podać identyfikator zasobu, nazwę użytkownika i hasło dla maszyny wirtualnej. Identyfikator zasobu maszyny Wirtualnej można konstruować przy użyciu identyfikatora subskrypcji, nazwy grupy zasobów i nazwy maszyny Wirtualnej przy użyciu następującego formatu ciągu:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Możesz też dołączyć dsvm do obszaru roboczego [za pomocą usługi Azure Machine Learning studio](#portal-reuse).

1. **Konfigurowanie:** Utwórz konfigurację uruchamiania dla obiektu docelowego obliczeń DSVM. Platformy Docker i conda są używane do tworzenia i konfigurowania środowiska szkoleniowego w systemie DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Teraz, gdy masz dołączone obliczenia i skonfigurowane uruchomienie, następnym krokiem jest [przesłanie przebiegu szkolenia](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Usługa Azure HDInsight to popularna platforma do analizy dużych zbiorów danych. Platforma zapewnia Apache Spark, który może służyć do szkolenia modelu.

1. **Utwórz:** Utwórz klaster HDInsight przed użyciem go do trenowania modelu. Aby utworzyć platformę Spark w klastrze HDInsight, zobacz [Tworzenie klastra platformy Spark w programie HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Podczas tworzenia klastra należy określić nazwę użytkownika i hasło SSH. Zanotuj te wartości, ponieważ są potrzebne do używania funkcji HDInsight jako celu obliczeniowego.
    
    Po utworzeniu klastra połącz się z \<nim za pomocą nazwy klastra nazwy \<hosta> ssh.azurehdinsight.net, gdzie nazwa klastra> jest nazwą podaną dla klastra. 

1. **Dołącz:** Aby dołączyć klaster HDInsight jako cel obliczeniowy, należy podać identyfikator zasobu, nazwę użytkownika i hasło dla klastra HDInsight. Identyfikator zasobu klastra HDInsight można konstruować przy użyciu identyfikatora subskrypcji, nazwy grupy zasobów i nazwy klastra HDInsight przy użyciu następującego formatu ciągu:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

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

   Możesz też dołączyć klaster HDInsight do obszaru roboczego [za pomocą studia usługi Azure Machine Learning.](#portal-reuse)

1. **Konfigurowanie:** Utwórz konfigurację uruchamiania dla obiektu docelowego obliczeń HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teraz, gdy masz dołączone obliczenia i skonfigurowane uruchomienie, następnym krokiem jest [przesłanie przebiegu szkolenia](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Usługa Azure Batch jest używana do wydajnego uruchamiania aplikacji komputerowych na dużą skalę w chmurze. AzureBatchStep może służyć w potoku usługi Azure Machine Learning do przesyłania zadań do puli partii Azure maszyn.

Aby dołączyć usługę Azure Batch jako miejsce docelowe obliczeń, należy użyć zestawu SDK usługi Azure Machine Learning i podać następujące informacje:

-    **Nazwa obliczeniowa usługi Azure Batch:** przyjazna nazwa używana dla obliczeń w obszarze roboczym
-    **Nazwa konta usługi Azure Batch**: Nazwa konta usługi Azure Batch
-    **Grupa zasobów:** Grupa zasobów zawierająca konto usługi Azure Batch.

Poniższy kod pokazuje, jak dołączyć usługę Azure Batch jako miejsce docelowe obliczeń:

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Konfigurowanie w studio usługi Azure Machine Learning

Można uzyskać dostęp do obiektów docelowych obliczeń, które są skojarzone z obszaru roboczego w studio usługi Azure Machine Learning.  Możesz użyć studia, aby:

* [Wyświetlanie obiektów docelowych obliczeń](#portal-view) dołączonych do obszaru roboczego
* [Tworzenie celu obliczeniowego](#portal-create) w obszarze roboczym
* [Dołączanie obiektu docelowego obliczeń](#portal-reuse) utworzonego poza obszarem roboczym


Po utworzeniu obiektu docelowego i dołączeniu go do obszaru roboczego `ComputeTarget` będzie on używany w konfiguracji uruchamiania z obiektem: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Wyświetlanie obiektów docelowych obliczeń


Aby wyświetlić cele obliczeniowe dla obszaru roboczego, należy wykonać następujące czynności:

1. Przejdź do [studia usługi Azure Machine Learning](https://ml.azure.com).
 
1. W obszarze __Aplikacje__wybierz pozycję __Oblicz__.

    [![Wyświetl kartę obliczeń](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Tworzenie celu obliczeniowego

Wykonaj poprzednie kroki, aby wyświetlić listę celów obliczeniowych. Następnie wykonaj następujące kroki, aby utworzyć obiekt docelowy obliczeń: 

1. Wybierz znak plus (+), aby dodać cel obliczeniowy.

    ![Dodawanie obiektu docelowego obliczeń](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Wprowadź nazwę obiektu docelowego obliczeń. 

1. Wybierz **opcję Uczenie maszynowe** jako typ obliczeń używanych do __szkolenia__. 

    >[!NOTE]
    >Obliczenia usługi Azure Machine Learning to jedyny zasób zarządzanych obliczeń, który można utworzyć w studio usługi Azure Machine Learning.  Wszystkie inne zasoby obliczeniowe mogą być dołączone po ich utworzeniu.

1. Wypełnij formularz. Podaj wartości dla wymaganych właściwości, zwłaszcza **rodziny maszyn wirtualnych**i **maksymalnych węzłów używanych** do rozkręcania obliczeń.  

1. Wybierz pozycję __Utwórz__.


1. Wyświetl stan operacji tworzenia, wybierając cel obliczeniowy z listy:

    ![Wybierz obiekt docelowy obliczeń, aby wyświetlić stan operacji tworzenia](./media/how-to-set-up-training-targets/View_list.png)

1. Następnie zostanie wyświetlane szczegóły dotyczące celu obliczeniowego: 

    ![Wyświetlanie szczegółów celu komputera](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Dołączanie obiektów docelowych obliczeń

Aby użyć obiektów docelowych obliczeń utworzonych poza obszarem roboczym usługi Azure Machine Learning, należy je dołączyć. Dołączanie obiektu docelowego obliczeń udostępnia go w obszarze roboczym.

Wykonaj kroki opisane wcześniej, aby wyświetlić listę celów obliczeniowych. Następnie należy wykonać następujące kroki, aby dołączyć obiekt docelowy obliczeń: 

1. Wybierz znak plus (+), aby dodać cel obliczeniowy. 
1. Wprowadź nazwę obiektu docelowego obliczeń. 
1. Wybierz typ obliczeń do dołączenia do __szkolenia:__

    > [!IMPORTANT]
    > Nie wszystkie typy obliczeń można dołączyć z usługi Azure Machine Learning studio. Typy obliczeń, które mogą być obecnie dołączone do szkolenia obejmują:
    >
    > * Zdalna maszyna wirtualna
    > * Usługa Azure Databricks (do użycia w potokach uczenia maszynowego)
    > * Usługa Azure Data Lake Analytics (do użycia w potokach uczenia maszynowego)
    > * Azure HDInsight

1. Wypełnij formularz i podaj wartości wymaganych właściwości.

    > [!NOTE]
    > Firma Microsoft zaleca używanie kluczy SSH, które są bezpieczniejsze niż hasła. Hasła są narażone na ataki siłowe. Klucze SSH opierają się na podpisach kryptograficznych. Aby uzyskać informacje na temat tworzenia kluczy SSH do użytku z maszynami wirtualnymi platformy Azure, zobacz następujące dokumenty:
    >
    > * [Tworzenie i używanie kluczy SSH w systemie Linux lub macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i używanie kluczy SSH w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wybierz __pozycję Dołącz__. 
1. Wyświetl stan operacji dołączania, wybierając obiekt docelowy obliczeń z listy.

## <a name="set-up-with-cli"></a>Konfigurowanie z pomocą interfejsu wiersza polecenia

Można uzyskać dostęp do obiektów docelowych obliczeń, które są skojarzone z obszaru roboczego przy użyciu [rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) dla usługi Azure Machine Learning.  Za pomocą interfejsu wiersza polecenia można:

* Tworzenie zarządzanego obiektu docelowego obliczeń
* Aktualizowanie zarządzanego obiektu docelowego obliczeń
* Dołączanie niezarządzanego obiektu docelowego obliczeń

Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Konfigurowanie za pomocą programu VS Code

Można uzyskać dostęp, utworzyć i zarządzać cele obliczeniowe, które są skojarzone z obszaru roboczego przy użyciu [rozszerzenia kodu VS](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) dla usługi Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Przesyłanie przebiegu szkoleniowego przy użyciu zestawu SDK usługi Azure Machine Learning

Po utworzeniu konfiguracji uruchamiania, należy użyć go do uruchomienia eksperymentu.  Wzorzec kodu do przesłania przebiegu szkolenia jest taki sam dla wszystkich typów obiektów docelowych obliczeń:

1. Tworzenie eksperymentu do uruchomienia
1. Prześlij bieg.
1. Poczekaj na zakończenie biegu.

> [!IMPORTANT]
> Podczas przesyłania przebiegu szkolenia, migawka katalogu, który zawiera skrypty szkolenia jest tworzony i wysyłany do obiektu docelowego obliczeń. Jest również przechowywany jako część eksperymentu w obszarze roboczym. Jeśli zmienisz pliki i ponownie prześlesz bieg, zostaną przesłane tylko zmienione pliki.
>
> Aby zapobiec doliczaniu plików do migawki, utwórz `.amlignore` [plik gitignore](https://git-scm.com/docs/gitignore) lub plik w katalogu i dodaj do niego pliki. Plik `.amlignore` używa tej samej składni i wzorców co plik [gitignore.](https://git-scm.com/docs/gitignore) Jeśli oba pliki `.amlignore` istnieją, plik ma pierwszeństwo.
> 
> Aby uzyskać więcej informacji, zobacz [Migawki](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Najpierw utwórz eksperyment w obszarze roboczym.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

Prześlij `ScriptRunConfig` eksperyment z obiektem.  Ten obiekt zawiera:

* **source_directory**: Katalog źródłowy zawierający skrypt szkoleniowy
* **skrypt**: Identyfikowanie skryptu szkoleniowego
* **run_config**: Konfiguracja uruchamiania, która z kolei określa, gdzie odbędzie się szkolenie.

Na przykład, aby użyć lokalnej konfiguracji [docelowej:](#local)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Przełącz ten sam eksperyment, aby uruchomić w innym miejscu docelowym obliczeń przy użyciu innej konfiguracji uruchamiania, takiej jak [amlcompute target:](#amlcompute)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> W tym przykładzie domyślnie przy użyciu tylko jeden węzeł obiektu docelowego obliczeń do szkolenia. Aby użyć więcej niż jednego `node_count` węzła, ustaw konfigurację uruchamiania na żądaną liczbę węzłów. Na przykład poniższy kod ustawia liczbę węzłów używanych do szkolenia do czterech:
>
> ```python
> src.run_config.node_count = 4
> ```

Możesz też:

* Prześlij `Estimator` eksperyment z obiektem, jak pokazano w [modelach train ml z estymatorami](how-to-train-ml-models.md).
* Prześlij uruchomienie hyperdrive do [dostrajania hiperparametry.](how-to-tune-hyperparameters.md)
* Prześlij eksperyment za pomocą [rozszerzenia kodu VS](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Aby uzyskać więcej informacji, zobacz [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) i [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) dokumentacji.

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Tworzenie konfiguracji uruchamiania i przesyłanie przebiegu przy użyciu interfejsu wiersza polecenia usługi Azure Machine Learning

Za pomocą [rozszerzenia interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i uczenia [maszynowego](reference-azure-machine-learning-cli.md) można utworzyć konfiguracje uruchamiania i przesyłać przebiegi na różnych obiektach docelowych obliczeń. Poniższe przykłady zakładają, że masz istniejący obszar roboczy usługi `az login` Azure Machine Learning i zalogowałeś się na platformie Azure przy użyciu polecenia CLI. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Tworzenie konfiguracji uruchamiania

Najprostszym sposobem utworzenia konfiguracji uruchamiania jest poruszanie się po folderze zawierającym skrypty języka Python uczenia maszynowego i używanie polecenia CLI

```azurecli
az ml folder attach
```

To polecenie tworzy podfolder, `.azureml` który zawiera pliki konfiguracji uruchamiania szablonu dla różnych obiektów docelowych obliczeń. Można skopiować i edytować te pliki, aby dostosować konfigurację, na przykład, aby dodać pakiety Python lub zmienić ustawienia platformy Docker.  

### <a name="structure-of-run-configuration-file"></a>Struktura uruchom plik konfiguracyjny

Uruchom plik konfiguracyjny jest sformatowany yaml, z następującymi sekcjami
 * Skrypt do uruchomienia i jego argumenty
 * Oblicz nazwę docelową , "lokalny" lub nazwę obliczeń w obszarze roboczym.
 * Parametry do wykonywania przebiegu: framework, komunikator dla rozproszonych przebiegów, maksymalny czas trwania i liczba węzłów obliczeniowych.
 * Środowiska. Zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych i zarządzanie nimi,](how-to-use-environments.md) aby uzyskać szczegółowe informacje na temat pól w tej sekcji.
   * Aby określić pakiety języka Python do zainstalowania dla uruchomienia, utwórz [plik środowiska conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)i ustaw pole __condaDependenciesFile.__
 * Uruchom szczegóły historii, aby określić folder pliku dziennika oraz włączyć lub wyłączyć zbieranie danych wyjściowych i uruchamiać migawki historii.
 * Szczegóły konfiguracji specyficzne dla wybranej struktury.
 * Szczegóły dotyczące danych i magazynu danych.
 * Szczegóły konfiguracji specyficzne dla obliczeń uczenia maszynowego do tworzenia nowego klastra.

Zobacz [przykładowy plik JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) dla pełnego schematu runconfig.

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Najpierw utwórz eksperyment dla swoich biegów

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Uruchomienie skryptu

Aby przesłać uruchomienie skryptu, wykonaj polecenie

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive uruchom

Za pomocą funkcji HyperDrive z platformą Azure CLI można wykonywać przebiegi dostrajania parametrów. Najpierw utwórz plik konfiguracyjny hyperdrive w następującym formacie. Zobacz [Tune hyperparameters dla modelu](how-to-tune-hyperparameters.md) artykuł, aby uzyskać szczegółowe informacje na temat parametrów dostrajania hiperparametryczne.

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

Dodaj ten plik wraz z uruchomionymi plikami konfiguracyjnymi. Następnie prześlij uruchomienie hyperdrive przy użyciu:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Zwróć uwagę na sekcję *argumentów* w runconfig i *spacji parametrów* w konfiguracji HyperDrive. Zawierają one argumenty wiersza polecenia, które mają być przekazywane do skryptu szkoleniowego. Wartość w runconfig pozostaje taka sama dla każdej iteracji, podczas gdy zakres w konfiguracji HyperDrive jest iterowany. Nie należy określać tego samego argumentu w obu plikach.

Aby uzyskać więcej ```az ml``` informacji na temat tych poleceń interfejsu wiersza polecenia i pełnego zestawu argumentów, zobacz [dokumentację referencyjną](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Śledzenie i integracja Git

Po uruchomieniu przebiegu szkolenia, w którym katalog źródłowy jest lokalnym repozytorium Git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Integracja git dla usługi Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Przykłady notesów

Zobacz te notesy, aby zapoznać się z przykładami szkoleń z różnymi celami obliczeniowymi:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriale/img-klasyfikacja-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Trenuj model](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do szkolenia modelu.
* Dowiedz się, jak [skutecznie dostrajać hiperparametry,](how-to-tune-hyperparameters.md) aby tworzyć lepsze modele.
* Po uzyskaniu wyszkolonego modelu dowiedz się, [jak i gdzie wdrożyć modele.](how-to-deploy-and-where.md)
* Służy do wyświetlania odwołania do [sdk sdk klasy RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)
* [Korzystanie z usługi Azure Machine Learning w sieciach wirtualnych platformy Azure](how-to-enable-virtual-network.md)
