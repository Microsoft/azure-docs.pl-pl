---
title: Tworzenie połączonej usługi przy użyciu usługi Synapse i Azure Machine Learning roboczych (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak połączyć Azure Synapse i Azure Machine Learning robocze w celu ujednoliconego środowiska przetwarzania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575742"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Łączenie Azure Synapse Analytics i Azure Machine Learning roboczych (wersja zapoznawcza)

Z tego artykułu dowiesz się, jak utworzyć usługę połączona, która łączy obszar roboczy Azure Synapse Analytics [z](/azure/synapse-analytics/overview-what-is) [Azure Machine Learning roboczym.](concept-workspace.md)

Dzięki obszarowi roboczemu usługi Azure Machine Learning połączonemu z obszarem roboczym usługi Azure Synapse możesz dołączyć pulę usługi Apache Spark jako dedykowaną moc obliczeniową do rozmieszczania danych na dużą skalę lub przeprowadzić trenowanie modelu z tego samego notesu języka Python.

Obszar roboczy uczenia maszynowego i obszar roboczy usługi Synapse można połączyć za pomocą zestawu SDK języka [Python](#link-sdk) lub [Azure Machine Learning studio](#link-studio).

Możesz również połączyć obszary robocze i dołączyć pulę usługi Synapse Spark za pomocą jednego [Azure Resource Manager (ARM).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)

>[!IMPORTANT]
> Integracja Azure Machine Learning i Azure Synapse jest w publicznej wersji zapoznawczej. Funkcje prezentowane w pakiecie są eksperymentalnymi funkcjami w wersji `azureml-synapse` zapoznawczej i mogą ulec zmianie w dowolnym momencie. [](/python/api/overview/azure/ml/#stable-vs-experimental)

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz obszar Azure Machine Learning roboczego.](how-to-manage-workspace.md?tabs=python)

* [Utwórz obszar roboczy synapse w Azure Portal](/azure/synapse-analytics/quickstart-create-workspace).

* [Tworzenie Apache Spark przy użyciu Azure Portal, narzędzi internetowych lub Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Instalowanie [zestawu SDK Azure Machine Learning Python](/python/api/overview/azure/ml/intro)

* Dostęp do [Azure Machine Learning studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Łączenie obszarów roboczych przy użyciu zestawu SDK języka Python

> [!IMPORTANT]
> Aby pomyślnie połączyć się z obszarem roboczym synapse, musisz mieć przyznaną **rolę właściciela** obszaru roboczego synapse. Sprawdź dostęp w [Azure Portal](https://ms.portal.azure.com/).
>
> Jeśli nie jesteś właścicielem **i** jesteś tylko współautorem obszaru roboczego usługi Synapse, możesz używać tylko istniejących połączonych usług.  Zobacz, jak [retrive i użyj istniejącej połączonej usługi](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

Poniższy kod wykorzystuje klasy [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) i [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) do,

* Połącz obszar roboczy uczenia maszynowego `ws` z Azure Synapse roboczym.
* Zarejestruj obszar roboczy usługi Synapse w Azure Machine Learning jako połączona usługa.

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
> Tożsamość zarządzana `system_assigned_identity_principal_id` jest tworzona dla każdej połączonej usługi. Ta tożsamość zarządzana musi mieć przyznaną rolę **administratora usługi Synapse Apache Spark** obszaru roboczego usługi Synapse przed rozpoczęciem sesji usługi Synapse. [Przypisz rolę administratora usługi Synapse Apache Spark do tożsamości zarządzanej](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)w Synapse Studio .
>
> Aby znaleźć `system_assigned_identity_principal_id` konkretną powiązaną usługę, użyj . `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')`

### <a name="manage-linked-services"></a>Zarządzanie połączonymi usługami

Wyświetl wszystko połączone usługi skojarzone z obszarem roboczym uczenia maszynowego.

```python
LinkedService.list(ws)
```

Aby odłączyć obszary robocze, użyj `unregister()` metody

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Łączenie obszarów roboczych za pośrednictwem programu Studio

Połącz obszar roboczy usługi Machine Learning i obszar roboczy usługi Synapse za pośrednictwem Azure Machine Learning studio, aby wykonać następujące kroki: 

1. Zaloguj się do [Azure Machine Learning studio](https://ml.azure.com/).
1. Wybierz **pozycję Połączone usługi** w sekcji **Zarządzanie** w okienku po lewej stronie.
1. Wybierz **pozycję Dodaj integrację.**
1. W **formularzu Łączenie obszaru roboczego** wypełnij pola

    |Pole| Opis    
    |---|---
    |Nazwa| Podaj nazwę połączonej usługi. Ta nazwa będzie używana do odwołania do tej konkretnej połączonej usługi.
    |Nazwa subskrypcji | Wybierz nazwę subskrypcji skojarzonej z obszarem roboczym uczenia maszynowego. 
    |Obszar roboczy synapse | Wybierz obszar roboczy synapse, z którym chcesz utworzyć link.
    
1. Wybierz **przycisk Dalej,** aby otworzyć **formularz Wybierz pule platformy Spark (opcjonalnie).** W tym formularzu możesz wybrać pulę synapse Spark do dołączenia do obszaru roboczego

1. Wybierz **przycisk** Dalej, aby otworzyć **formularz Przegląd** i sprawdzić wybrane opcje.
1. Wybierz **pozycję Utwórz,** aby ukończyć proces tworzenia połączonej usługi.

## <a name="next-steps"></a>Następne kroki

* [Dołączanie pul synapse Spark w celu przygotowania danych za pomocą Azure Synapse (wersja zapoznawcza)](how-to-data-prep-synapse-spark-pool.md).
* [Jak używać aplikacji Apache Spark potoku uczenia maszynowego za pomocą Azure Synapse (wersja zapoznawcza)](how-to-use-synapsesparkstep.md)
* [Trenowanie modelu.](how-to-set-up-training-targets.md)
