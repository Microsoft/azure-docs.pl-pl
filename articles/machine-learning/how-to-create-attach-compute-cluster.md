---
title: Tworzenie klastrów obliczeniowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć klastry obliczeniowe w obszarze roboczym Azure Machine Learning. Użyj klastra obliczeniowego jako elementu docelowego obliczeń do szkolenia lub wnioskowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 0bbf70016dc9b93120b3158e8954c336095ea211
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832691"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Tworzenie klastra obliczeniowego Azure Machine Learning

Dowiedz się, jak utworzyć [klaster obliczeniowy](concept-compute-target.md#azure-machine-learning-compute-managed) i zarządzać nim w obszarze roboczym Azure Machine Learning.

Za pomocą Azure Machine Learning klastra obliczeniowego można rozesłać proces uczenia lub przetwarzania wsadowego za pośrednictwem klastra węzłów obliczeniowych procesora CPU lub procesora GPU w chmurze. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, które obejmują procesory GPU, zobacz [rozmiary maszyny wirtualnej zoptymalizowanej według procesora GPU](../virtual-machines/sizes-gpu.md). 

W tym artykule dowiesz się, jak:

* Tworzenie klastra obliczeniowego
* Obniż koszt klastra obliczeniowego
* Skonfiguruj [zarządzaną tożsamość](../active-directory/managed-identities-azure-resources/overview.md) klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>Co to jest klaster obliczeniowy?

Azure Machine Learning klaster obliczeniowy to infrastruktura obliczeniowa, która umożliwia łatwe tworzenie obliczeń jednego lub wielowęzłowego. Obliczenia są tworzone w regionie obszaru roboczego jako zasób, który może być współużytkowany z innymi użytkownikami w obszarze roboczym. Obliczenia są skalowane automatycznie podczas przesyłania zadania i mogą być umieszczane w Virtual Network platformy Azure. Obliczenia są wykonywane w środowisku kontenerowym i pakiety zależności modelu w [kontenerze platformy Docker](https://www.docker.com/why-docker).

Klastry obliczeniowe mogą bezpiecznie uruchamiać zadania w [środowisku sieci wirtualnej](how-to-secure-training-vnet.md), bez konieczności otwierania portów SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku kontenerowym i pakuje zależności modelu w kontenerze platformy Docker. 

## <a name="limitations"></a>Ograniczenia

* **Nie należy tworzyć wielu jednoczesnych załączników do tego samego obliczenia** w obszarze roboczym. Można na przykład dołączyć jeden klaster obliczeniowy do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.

    Jeśli chcesz ponownie dołączyć obiekt docelowy obliczeń, na przykład aby zmienić ustawienia konfiguracji klastra, musisz najpierw usunąć istniejący załącznik.

* Niektóre scenariusze wymienione w tym dokumencie są oznaczone jako __wersja zapoznawcza__. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Azure Machine Learning COMPUTE ma limity domyślne, takie jak liczba rdzeni, które można przydzielić. Aby uzyskać więcej informacji, zobacz [Zarządzanie przydziałami zasobów platformy Azure i ich żądania](how-to-manage-quotas.md).

* System Azure pozwala na umieszczenie _blokad_ w zasobach, dzięki czemu nie można ich usunąć lub tylko do odczytu. __Nie stosuj blokad zasobów do grupy zasobów, która zawiera obszar roboczy__. Zastosowanie blokady do grupy zasobów, która zawiera obszar roboczy, uniemożliwi operacje skalowania klastrów obliczeniowych platformy Azure ML. Aby uzyskać więcej informacji na temat blokowania zasobów, zobacz [blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Klastry mogą zwykle skalować do 100 węzłów, o ile masz wystarczające limity przydziału dla wymaganej liczby rdzeni. Domyślnie klastry są skonfigurowane z obsługą komunikacji między węzłami między węzłami klastra w celu obsługi zadań MPI na przykład. Można jednak skalować klastry do tysięcy węzłów, po prostu [zwiększając bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)i żądając zezwolenia na wyświetlenie listy subskrypcji lub obszaru roboczego lub określonego klastra w celu wyłączenia komunikacji między węzłami. 


## <a name="create"></a>Utwórz

**Szacowany czas**: około 5 minut.

Azure Machine Learning obliczeń można użyć ponownie w ramach przebiegów. Obliczenia mogą być współużytkowane z innymi użytkownikami w obszarze roboczym i zachowywane między działami, automatyczne skalowanie węzłów w górę lub w dół na podstawie liczby przesłanych przebiegów oraz max_nodes ustawionych w klastrze. Ustawienie min_nodes steruje minimalnymi dostępnymi węzłami.

Dedykowane rdzenie dla poszczególnych regionów na poszczególne maszyny i łączne limity przydziału regionalnego, które mają zastosowanie do tworzenia klastra obliczeniowego, są ujednolicone i udostępniane w ramach przydziału wystąpienia obliczeniowego Azure Machine Learning. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Obliczenia są skalowane automatycznie do zerowych węzłów, gdy nie są używane.   Dedykowane maszyny wirtualne są tworzone w celu uruchamiania zadań zgodnie z potrzebami.
    
# <a name="python"></a>[Python](#tab/python)

Aby utworzyć trwały Azure Machine Learning zasobów obliczeniowych w języku Python, określ właściwości **vm_size** i **max_nodes** . Azure Machine Learning następnie używa inteligentnych ustawień domyślnych dla innych właściwości. 
    
* **vm_size**: rodzina maszyn wirtualnych węzłów utworzonych przez Azure Machine Learning COMPUTE.
* **max_nodes**: Maksymalna liczba węzłów do automatycznego skalowania w górę do momentu uruchomienia zadania na Azure Machine Learning obliczeń.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Podczas tworzenia Azure Machine Learning obliczeń można także skonfigurować kilka zaawansowanych właściwości. Właściwości umożliwiają tworzenie trwałego klastra o stałym rozmiarze lub w ramach istniejącej Virtual Network platformy Azure w ramach subskrypcji.  Aby uzyskać szczegółowe informacje, zobacz [klasę AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) .


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Aby uzyskać informacje na temat tworzenia klastra obliczeniowego w programie Studio, zobacz [Tworzenie obiektów docelowych obliczeń w programie Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Obniż koszt klastra obliczeniowego

Możesz również użyć [maszyn wirtualnych o niskim priorytecie](concept-plan-manage-cost.md#low-pri-vm) do uruchamiania niektórych lub wszystkich obciążeń. Te maszyny wirtualne nie mają gwarantowanej dostępności i mogą być przeszukiwane w trakcie użytkowania. Zastępujące zadanie zostało ponownie uruchomione, a nie wznowione. 

Użyj dowolnego z tych metod, aby określić maszynę wirtualną o niskim priorytecie:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Ustaw `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

W programie Studio wybierz pozycję **niski priorytet** podczas tworzenia maszyny wirtualnej.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurowanie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Skonfiguruj zarządzaną tożsamość w konfiguracji aprowizacji:  

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

* Dodawanie tożsamości zarządzanej do istniejącego klastra obliczeniowego 
    
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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

* Utwórz nowy zarządzany klaster obliczeniowy z zarządzaną tożsamością

  * Tożsamość zarządzana przypisana przez użytkownika

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Tożsamość zarządzana przypisana przez system

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Dodaj zarządzaną tożsamość do istniejącego klastra:

    * Tożsamość zarządzana przypisana przez użytkownika
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Tożsamość zarządzana przypisana przez system

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Zobacz [Konfigurowanie tożsamości zarządzanej w programie Studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Użycie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>Następne kroki

Użyj klastra obliczeniowego, aby:

* [Prześlij przebieg szkoleniowy](how-to-set-up-training-targets.md) 
* [Uruchom wnioskowanie wsadowe](./tutorial-pipeline-batch-scoring-classification.md).
