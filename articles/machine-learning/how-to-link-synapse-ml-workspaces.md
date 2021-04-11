---
title: Tworzenie połączonej usługi z Synapse i obszarami roboczymi Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak połączyć obszary robocze usługi Azure Synapse i Azure Machine Learning, aby uzyskać ujednolicone środowisko danych przetwarzanie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 511ee1aa5f5036f5ca5450def0e4481c0608db33
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227351"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Łączenie obszarów roboczych usługi Azure Synapse Analytics i Azure Machine Learningych (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć połączoną usługę, która łączy obszar roboczy [usługi Azure Synapse Analytics](/azure/synapse-analytics/overview-what-is) i [obszar roboczy Azure Machine Learning](concept-workspace.md).

Korzystając z Azure Machine Learningego obszaru roboczego połączonego z obszarem roboczym usługi Azure Synapse, możesz dołączyć pulę Apache Spark jako dedykowane obliczenia dla danych przetwarzanie w skali i przeprowadzić szkolenia modeli z tego samego notesu.

Obszar roboczy ML i obszar roboczy Synapse można połączyć za pomocą [zestawu SDK języka Python](#link-sdk) lub [Azure Machine Learning Studio](#link-studio).

Możesz również łączyć obszary robocze i dołączać pulę Synapse Spark z jednym [szablonem Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> Integracja Azure Machine Learning i Azure Synapse jest w publicznej wersji zapoznawczej. Funkcje przedstawione w `azureml-synapse` pakiecie to [eksperymentalna](/python/api/overview/azure/ml/#stable-vs-experimental) funkcja w wersji zapoznawczej i może ulec zmianie w dowolnym momencie.

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Utwórz obszar roboczy Synapse w Azure Portal](/azure/synapse-analytics/quickstart-create-workspace).

* [Tworzenie puli Apache Spark przy użyciu Azure Portal, narzędzi sieci Web lub Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Zainstaluj [zestaw Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)

* Dostęp do [Azure Machine Learning Studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Łączenie obszarów roboczych z zestawem SDK języka Python

> [!IMPORTANT]
> Aby pomyślnie połączyć się z obszarem roboczym Synapse, musisz mieć przyznany rola **właściciela** obszaru roboczego Synapse. Sprawdź swój dostęp w [Azure Portal](https://ms.portal.azure.com/).
>
> Jeśli nie jesteś **właścicielem** i jesteś tylko **współautorem** obszaru roboczego Synapse, możesz korzystać tylko z istniejących połączonych usług. Zobacz, jak [pobrać i użyć istniejącej połączonej usługi](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

Poniższy kod wykorzystuje [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) klasy i do,

* Połącz obszar roboczy usługi Machine Learning `ws` z obszarem roboczym Azure Synapse.
* Zarejestruj obszar roboczy Synapse, korzystając z Azure Machine Learning jako połączonej usługi.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Tożsamość zarządzana `system_assigned_identity_principal_id` jest tworzona dla każdej połączonej usługi. Ta tożsamość zarządzana musi mieć przyznaną rolę **administratora Synapse Apache Spark** w obszarze roboczym Synapse przed rozpoczęciem sesji Synapse. [Przypisz rolę administratora Synapse Apache Spark do tożsamości zarządzanej w programie Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Aby znaleźć `system_assigned_identity_principal_id` określoną połączoną usługę, użyj `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Zarządzanie połączonymi usługami

Wyświetl wszystkie połączone usługi powiązane z obszarem roboczym usługi Machine Learning.

```python
LinkedService.list(ws)
```

Aby odłączyć obszary robocze, użyj `unregister()` metody

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Łączenie obszarów roboczych za pośrednictwem programu Studio

Połącz obszar roboczy uczenia maszynowego i obszar roboczy Synapse za pomocą programu Azure Machine Learning Studio, wykonując następujące czynności: 

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. W sekcji **Zarządzanie** w okienku po lewej stronie wybierz pozycję **połączone usługi** .
1. Wybierz pozycję **Dodaj integrację**.
1. W formularzu **Połącz obszar roboczy** Wypełnij pola.

    |Pole| Opis    
    |---|---
    |Nazwa| Podaj nazwę połączonej usługi. Ta nazwa jest używana do odwoływania się do tej konkretnej połączonej usługi.
    |Nazwa subskrypcji | Wybierz nazwę subskrypcji, która jest skojarzona z obszarem roboczym usługi Machine Learning. 
    |Obszar roboczy Synapse | Wybierz obszar roboczy Synapse, do którego chcesz utworzyć łącze.
    
1. Wybierz pozycję **dalej** , aby otworzyć formularz **Wybieranie pul platformy Spark (opcjonalnie)** . Na tym formularzu wybierz pulę Synapse Spark do dołączenia do obszaru roboczego

1. Wybierz pozycję **dalej** , aby otworzyć formularz **Recenzja** i sprawdzić wybrane opcje.
1. Wybierz pozycję **Utwórz** , aby zakończyć proces tworzenia połączonej usługi.

## <a name="next-steps"></a>Następne kroki

* [Dołącz pule Synapse Spark na potrzeby przygotowywania danych za pomocą usługi Azure Synapse (wersja zapoznawcza)](how-to-data-prep-synapse-spark-pool.md).
* [Jak używać Apache Spark w potoku uczenia maszynowego przy użyciu usługi Azure Synapse (wersja zapoznawcza)](how-to-use-synapsesparkstep.md)
* [Uczenie modelu](how-to-set-up-training-targets.md).
