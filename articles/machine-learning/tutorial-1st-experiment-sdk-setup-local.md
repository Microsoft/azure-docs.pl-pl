---
title: 'Samouczek: wprowadzenie do uczenia maszynowego — Python'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nastąpi rozpoczęcie pracy z zestawem SDK środowiska Azure Machine Learning Python uruchomionym w osobistym środowisku programistycznym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946569"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Samouczek: wprowadzenie do Azure Machine Learning w środowisku deweloperskim (część 1 z 4)

W tej **serii samouczków z czterema częściami**przedstawiono podstawowe informacje o Azure Machine Learning i ukończeniu zadań w języku Python ml opartych na zadaniach w chmurze platformy Azure, w tym:

1. Skonfiguruj obszar roboczy i środowisko deweloperskie lokalnego uczenia maszynowego.
2. Uruchom kod w chmurze przy użyciu zestawu SDK języka Python Azure Machine Learning.
3. Zarządzaj środowiskiem Python używanym do szkolenia modeli.
4. Przekazuj dane na platformę Azure i korzystaj z nich w szkoleniu.

W **części 1 tej serii samouczków**będziesz:

> [!div class="checklist"]
> * Instalowanie zestawu SDK Azure Machine Learning
> * Konfigurowanie struktury katalogów dla kodu
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Konfigurowanie lokalnego środowiska programistycznego
> * Konfigurowanie klastra obliczeniowego

>[!NOTE]
> W tej serii samouczków omówiono Azure Machine Learning koncepcje dostosowane do zadań uczenia maszynowego __opartych na zadaniach__ w języku Python, które są pracochłonne i/lub wymagają odtwarzalności. Jeśli zadania uczenia maszynowego nie pasują do tego profilu, użyj [funkcji Jupyter lub RStudio na wystąpieniu obliczeniowym Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md) , aby dołączyć do Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.
- Znajomość [pojęć związanych](concept-azure-machine-learning-architecture.md)z językiem Python i Machine Learning. Na przykład środowiska, szkolenia, ocenianie i tak dalej.
- Lokalne środowisko programistyczne — laptop z zainstalowaną funkcją Python i Twoje ulubione środowisko IDE (na przykład: programu vscode, platformy PyCharm itd, Jupyter itd.).

## <a name="install-the-azure-machine-learning-sdk"></a>Instalowanie zestawu SDK Azure Machine Learning

W tym samouczku użyjemy Azure Machine Learning zestawu SDK języka Python.

Możesz korzystać z najpopularniejszych narzędzi — na przykład: Conda, PIP i tak dalej — w celu skonfigurowania środowiska do użycia w tym samouczku. Zainstaluj środowisko Azure Machine Learning zestawu SDK języka Python za pomocą narzędzia PIP:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Tworzenie struktury katalogów dla kodu
Zalecamy, aby dla tego samouczka był skonfigurowany następujący prosty katalog:

```markdown
tutorial
└──.azureml
```

- **samouczek** (katalog najwyższego poziomu projektu)
- **. Azure** (ukryty podkatalog): `.azureml` katalog jest używany do przechowywania plików konfiguracji Azure Machine Learning.

## <a name="create-an-azure-machine-learning-workspace"></a>Tworzenie Obszar roboczy usługi Azure Machine Learning

Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning i jest scentralizowanym miejscem do wykonania:

- Zarządzanie zasobami, takimi jak obliczenia
- Przechowywanie zasobów, takich jak notesy, środowiska, zestawy danych, potoki, modele, punkty końcowe itd.
- Współpraca z innymi członkami zespołu

W katalogu najwyższego poziomu nadrzędnego — `tutorial` Dodaj nowy plik w języku Python o nazwie `01-create-workspace.py` z poniższym kodem. Dostosowuj parametry (nazwę, Identyfikator subskrypcji, grupę zasobów i [lokalizację](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) z preferencjami.

Kod można uruchomić w sesji interaktywnej lub jako plik języka Python.

>[!NOTE]
> W przypadku korzystania z lokalnego środowiska projektowego (np. laptop) użytkownik zostanie poproszony o uwierzytelnienie w obszarze roboczym przy użyciu *kodu urządzenia* podczas pierwszego wykonywania poniższego kodu. Wykonaj instrukcje wyświetlane na ekranie.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Uruchom ten kod z `tutorial` katalogu:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

Po uruchomieniu powyższego fragmentu kodu struktura folderów będzie wyglądać następująco:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Plik `.azureml/config.json` zawiera metadane niezbędne do nawiązania połączenia z obszarem roboczym Azure Machine Learning — w tym identyfikator subskrypcji, grupę zasobów i nazwę obszaru roboczego. 

> [!NOTE]
> Zawartość `config.json` nie jest tajemnicą — dobrze jest udostępnić te szczegóły.
> Uwierzytelnianie jest nadal wymagane do współdziałania z obszarem roboczym Azure Machine Learning.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Tworzenie klastra obliczeniowego Azure Machine Learning

Utwórz skrypt języka Python w `tutorial` katalogu najwyższego poziomu o nazwie `02-create-compute.py` i wypełnij przy użyciu następującego kodu, aby utworzyć Azure Machine Learning klaster obliczeniowy, który będzie automatycznie skalowany między zerem i czterema węzłami:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Uruchom plik języka Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Po utworzeniu klastra nie będzie dostępna żadna z nich węzły. W związku z tym **klaster nie** ponosi kosztów, dopóki nie zostanie przesłane zadanie. Ten klaster zostanie przeskalowany w dół, gdy będzie bezczynny przez 2400 sekund (40 minut).

Struktura folderów będzie teraz wyglądać następująco:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku Instalatora:

- Utworzono obszar roboczy Azure Machine Learning
- Konfigurowanie lokalnego środowiska programistycznego
- Utworzono klaster obliczeniowy Azure Machine Learning.

W następnym samouczku pokazano, jak przesłać skrypt do klastra obliczeniowego Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie skryptu Python "Hello world" na platformie Azure](tutorial-1st-experiment-hello-world.md)
