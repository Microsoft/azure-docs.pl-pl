---
title: Tworzenie klastrów obliczeniowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć klastry obliczeniowe w Azure Machine Learning roboczym. Użyj klastra obliczeniowego jako docelowego obiektu obliczeniowego do trenowania lub wnioskowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: f61936e622a539b29c6788f631df5de42bb2f242
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861251"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Tworzenie klastra obliczeniowego Azure Machine Learning obliczeniowego

Dowiedz się, jak utworzyć klaster obliczeniowy i [zarządzać](concept-compute-target.md#azure-machine-learning-compute-managed) Azure Machine Learning roboczym.

Klaster obliczeniowy Azure Machine Learning do dystrybucji procesu trenowania lub wnioskowania wsadowego w klastrze węzłów obliczeniowych procesora CPU lub procesora GPU w chmurze. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, które obejmują procesory GPU, zobacz Rozmiary maszyn wirtualnych zoptymalizowanych pod kątem [procesora GPU.](../virtual-machines/sizes-gpu.md) 

Z tego artykułu dowiesz się, jak:

* Tworzenie klastra obliczeniowego
* Obniżenie kosztów klastra obliczeniowego
* Konfigurowanie tożsamości [zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)

* Rozszerzenie [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md)platformy Azure Machine Learning service , Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)lub rozszerzenie [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

* Jeśli używasz zestawu SDK języka Python, [skonfiguruj środowisko projektowe przy użyciu obszaru roboczego](how-to-configure-environment.md).  Po skonfigurowaniu środowiska dołącz do obszaru roboczego w skrypcie języka Python:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Co to jest klaster obliczeniowy?

Azure Machine Learning obliczeniowy to zarządzana infrastruktura obliczeniowa, która umożliwia łatwe tworzenie obliczeń z jednym lub wieloma węzłami. Zasoby obliczeniowe są tworzone w regionie obszaru roboczego jako zasób, który można udostępniać innym użytkownikom w obszarze roboczym. Obliczenia są skalowane automatycznie po przesłaniu zadania i można je umieścić w usłudze Azure Virtual Network. Środowisko obliczeniowe jest wykonywane w środowisku konteneryzowym i pakuje zależności modelu w [kontenerze platformy Docker.](https://www.docker.com/why-docker)

Klastry obliczeniowe mogą bezpiecznie uruchamiać zadania w środowisku sieci [wirtualnej](how-to-secure-training-vnet.md)bez konieczności otwierania portów SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku konteneryzowanych i pakuje zależności modelu w kontenerze platformy Docker. 

## <a name="limitations"></a>Ograniczenia

* Niektóre scenariusze wymienione w tym dokumencie są oznaczone jako wersja __zapoznawcza__. Funkcje w wersji zapoznawczej są udostępniane bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Obecnie obsługujemy tylko tworzenie (a nie aktualizowanie) klastrów za pomocą szablonów usługi ARM [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ]. Na razie zalecamy aktualizowanie zasobów obliczeniowych przy użyciu zestawu SDK, interfejsu wiersza polecenia lub środowiska użytkownika.

* Azure Machine Learning Compute ma domyślne limity, takie jak liczba rdzeni, które można przydzielić. Aby uzyskać więcej informacji, zobacz Manage and request quotas for Azure resources (Zarządzanie przydziałami zasobów [platformy Azure i żądanie ich przydziałów).](how-to-manage-quotas.md)

* Platforma Azure umożliwia blokowanie _zasobów,_ dzięki czemu nie można ich usunąć ani tylko do odczytu. __Nie należy stosować blokad zasobów do grupy zasobów zawierającej obszar roboczy__. Zastosowanie blokady do grupy zasobów zawierającej obszar roboczy uniemożliwi operacje skalowania dla klastrów obliczeniowych usługi Azure ML. Aby uzyskać więcej informacji na temat blokowania zasobów, zobacz [Blokowanie zasobów w celu zapobiegania nieoczekiwanym zmianom.](../azure-resource-manager/management/lock-resources.md)

> [!TIP]
> Klastry można zwykle skalować w górę do 100 węzłów, o ile masz wystarczający limit przydziału dla wymaganej liczby rdzeni. Domyślnie klastry są konfigurowany z włączoną komunikacją między węzłami między węzłami klastra w celu obsługi na przykład zadań MPI. Można jednak skalować klastry do 1000 [](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)węzłów, po prostu przez zgłoszenie biletu pomocy technicznej i żądanie, aby zezwolić na listę subskrypcji, obszaru roboczego lub określonego klastra w celu wyłączenia komunikacji między węzłami.


## <a name="create"></a>Utwórz

**Szacowany czas:** około 5 minut.

Azure Machine Learning obliczeniowe mogą być ponownie używane we wszystkich przebiegach. Obliczenia mogą być udostępniane innym użytkownikom w obszarze roboczym i są przechowywane między przebiegami, automatycznie skalując węzły w górę lub w dół na podstawie liczby przesłanych przebiegów i max_nodes ustawionych w klastrze. Ustawienie min_nodes określa minimalną dostępną ilość dostępnych węzłów.

Dedykowane rdzenie na region na limit przydziału rodziny maszyn wirtualnych i całkowity limit przydziału regionalnego, który ma zastosowanie do tworzenia klastra obliczeniowego, są ujednolicone i współdzielone z Azure Machine Learning przydziału trenowania wystąpień obliczeniowych. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Obliczenia są automatycznie skalowane w dół do zera węzłów, gdy nie są używane.   Dedykowane maszyny wirtualne są tworzone w celu uruchamiania zadań zgodnie z potrzebami.
    
# <a name="python"></a>[Python](#tab/python)


Aby utworzyć trwały zasób Azure Machine Learning Compute w języku Python, określ vm_size **i** **max_nodes** obliczeniowe. Azure Machine Learning następnie używa domyślnych ustawień inteligentnych dla innych właściwości.
    
* **vm_size:** rodzina maszyn wirtualnych węzłów utworzonych przez usługę Azure Machine Learning Compute.
* **max_nodes:** maksymalna liczba węzłów do automatycznego skalowania do czasu uruchomienia zadania w Azure Machine Learning Compute.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Możesz również skonfigurować kilka zaawansowanych właściwości podczas tworzenia Azure Machine Learning Compute. Właściwości umożliwiają utworzenie trwałego klastra o stałym rozmiarze lub w ramach istniejącej usługi Azure Virtual Network subskrypcji.  Aby uzyskać szczegółowe [informacje, zobacz klasę AmlCompute.](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Aby uzyskać więcej informacji, zobacz [az ml computetarget create amlcompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Aby uzyskać informacje na temat tworzenia klastra obliczeniowego w studio, zobacz Create compute targets in Azure Machine Learning studio (Tworzenie docelowych obiektów obliczeniowych w [programie Azure Machine Learning studio).](how-to-create-attach-compute-studio.md#amlcompute)

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Obniżenie kosztów klastra obliczeniowego

Możesz również użyć maszyn wirtualnych o niskim [priorytecie](concept-plan-manage-cost.md#low-pri-vm) do uruchamiania niektórych lub wszystkich obciążeń. Te maszyny wirtualne nie mają gwarantowanej dostępności i mogą zostać wywłaszone podczas używania. Konieczne będzie ponowne uruchomienie wywłaszczego zadania. 

Użyj dowolnego z tych sposobów, aby określić maszynę wirtualną o niskim priorytecie:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Ustaw wartość `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

W studio wybierz pozycję **Niski priorytet** podczas tworzenia maszyny wirtualnej.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurowanie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Konfigurowanie tożsamości zarządzanej w konfiguracji aprowizowania:  

    * Tożsamość zarządzana przypisana przez system utworzona w obszarze roboczym o nazwie `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Tożsamość zarządzana przypisana przez użytkownika utworzona w obszarze roboczym o nazwie `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Dodawanie tożsamości zarządzanej do istniejącego klastra obliczeniowego o nazwie `cpu_cluster`
    
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

* Tworzenie nowego zarządzanego klastra obliczeniowego z tożsamością zarządzaną

  * Tożsamość zarządzana przypisana przez użytkownika

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Tożsamość zarządzana przypisana przez system

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Dodaj tożsamość zarządzaną do istniejącego klastra:

    * Tożsamość zarządzana przypisana przez użytkownika
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Tożsamość zarządzana przypisana przez system

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Zobacz [Konfigurowanie tożsamości zarządzanej w programie Studio.](how-to-create-attach-compute-studio.md#managed-identity)

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Użycie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Istnieje możliwość, że niektórzy użytkownicy, którzy utworzyli swój obszar roboczy Azure Machine Learning z witryny Azure Portal przed wydaniem wersji ga Azure Portal mogą nie być w stanie utworzyć w tym obszarze roboczym aplikacji AmlCompute. Możesz poprosić o pomoc techniczną usługi lub utworzyć nowy obszar roboczy za pośrednictwem portalu lub zestawu SDK, aby natychmiast się odblokować.

Jeśli twój Azure Machine Learning obliczeniowy jest zablokowany podczas zmiany rozmiaru (od 0 do > 0) dla stanu węzła, może to być spowodowane blokadami zasobów platformy Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Następne kroki

Korzystanie z klastra obliczeniowego w celu:

* [Przesyłanie uruchomienia trenowania](how-to-set-up-training-targets.md) 
* [Uruchom wnioskowanie wsadowe](./tutorial-pipeline-batch-scoring-classification.md).
