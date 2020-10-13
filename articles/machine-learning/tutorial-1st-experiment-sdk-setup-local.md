---
title: 'Samouczek: wprowadzenie do uczenia maszynowego — Python'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nastąpi rozpoczęcie pracy z zestawem SDK Azure Machine Learning dla języka Python działającego w osobistym środowisku programistycznym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: c0fe3c3808709de732bec8ce0599d380094405e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91368485"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Samouczek: wprowadzenie do Azure Machine Learning w środowisku programistycznym (część 1 z 4)

W tej *serii samouczków z czterema częściami*przedstawiono podstawy Azure Machine Learning i kończenia zadań uczenia maszynowego w języku Python na platformie Azure w chmurze. Do zadań tych należą:

1. Skonfiguruj obszar roboczy i lokalne środowisko deweloperskie na potrzeby uczenia maszynowego.
2. Uruchom kod w chmurze za pomocą zestawu SDK Azure Machine Learning dla języka Python.
3. Zarządzaj środowiskiem Python używanym do szkolenia modeli.
4. Przekazuj dane na platformę Azure i korzystaj z nich w szkoleniu.

W części 1 tej serii samouczków będziesz:

> [!div class="checklist"]
> * Zainstaluj zestaw SDK Azure Machine Learning.
> * Skonfiguruj strukturę katalogów dla kodu.
> * Utwórz obszar roboczy Azure Machine Learning.
> * Skonfiguruj lokalne środowisko deweloperskie.
> * Skonfiguruj klaster obliczeniowy.

>[!NOTE]
> W tej serii samouczków omówiono Azure Machine Learning koncepcje dostosowane do zadań uczenia maszynowego *opartych na zadaniach* w języku Python, które są pracochłonne i/lub wymagają odtwarzalności. Jeśli zadania uczenia maszynowego nie pasują do tego profilu, użyj [funkcji Jupyter lub RStudio na wystąpieniu obliczeniowym Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md) , aby przenieść się do Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Spróbuj [Azure Machine Learning](https://aka.ms/AMLFree).
- Znajomość [pojęć związanych](concept-azure-machine-learning-architecture.md)z językiem Python i Machine Learning. Przykłady obejmują środowiska, szkolenia i ocenianie.
- Lokalne środowisko programistyczne: laptop z zainstalowaną funkcją Python i Twoje ulubione środowisko IDE (na przykład Visual Studio Code, platformy PyCharm itd lub Jupyter).

## <a name="install-the-azure-machine-learning-sdk"></a>Instalowanie zestawu SDK Azure Machine Learning

W tym samouczku użyjemy zestawu Azure Machine Learning SDK dla języka Python.

Możesz użyć najpopularniejszych narzędzi (na przykład Conda i PIP), aby skonfigurować środowisko do użycia w tym samouczku. Zainstaluj środowisko Azure Machine Learning zestawu SDK dla języka Python za pomocą narzędzia PIP:

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>Tworzenie struktury katalogów dla kodu
Zalecamy skonfigurowanie następującej prostej struktury katalogów dla tego samouczka:

```markdown
tutorial
└──.azureml
```

- `tutorial`: Katalog najwyższego poziomu projektu.
- `.azureml`: Ukryty podkatalog do przechowywania plików konfiguracji Azure Machine Learning.

## <a name="create-an-azure-machine-learning-workspace"></a>Tworzenie obszaru roboczego usługi Azure Machine Learning

Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning i jest scentralizowanym miejscem do wykonania:

- Zarządzaj zasobami, takimi jak obliczenia.
- Przechowuj zasoby, takie jak notesy, środowiska, zestawy danych, potoki, modele i punkty końcowe.
- Współpracuj z innymi członkami zespołu.

W katalogu najwyższego poziomu `tutorial` Dodaj nowy plik Python o nazwie przy `01-create-workspace.py` użyciu następującego kodu. Dostosuj parametry (nazwa, Identyfikator subskrypcji, Grupa zasobów i [Lokalizacja](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) z preferencjami.

Kod można uruchomić w sesji interaktywnej lub w postaci pliku języka Python.

>[!NOTE]
> Jeśli używasz lokalnego środowiska programistycznego (na przykład laptopu), zostanie wyświetlony monit o uwierzytelnienie w obszarze roboczym przy użyciu *kodu urządzenia* przy pierwszym uruchomieniu poniższego kodu. Wykonaj instrukcje wyświetlane na ekranie.

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

Po uruchomieniu poprzedniego fragmentu kodu struktura folderów będzie wyglądać następująco:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Plik `.azureml/config.json` zawiera metadane niezbędne do nawiązania połączenia z obszarem roboczym Azure Machine Learning. A mianowicie zawiera identyfikator subskrypcji, grupę zasobów i nazwę obszaru roboczego. 

> [!NOTE]
> Zawartość `config.json` nie jest wpisami tajnymi. Warto udostępnić te szczegóły.
>
> Uwierzytelnianie jest nadal wymagane do współdziałania z obszarem roboczym Azure Machine Learning.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Tworzenie klastra obliczeniowego Azure Machine Learning

Utwórz skrypt języka Python w `tutorial` katalogu najwyższego poziomu o nazwie `02-create-compute.py` . Wypełnij następujący kod, aby utworzyć Azure Machine Learning klaster obliczeniowy, który będzie automatycznie skalowany między zerem i czterema węzłami:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
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
> Po utworzeniu klastra zostanie zainicjowana obsługa 0 węzłów. W klastrze *nie są* naliczane koszty, dopóki nie zostanie przesłane zadanie. Ten klaster zostanie przeskalowany w dół, gdy będzie bezczynny przez 2 400 sekund (40 minut).

Struktura folderów będzie teraz wyglądać następująco:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku instalacji masz:

- Utworzono obszar roboczy Azure Machine Learning.
- Skonfiguruj lokalne środowisko programistyczne.
- Utworzono klaster obliczeniowy Azure Machine Learning.

W następnym samouczku pokazano, jak przesłać skrypt do klastra obliczeniowego Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie "Hello World!" Skrypt języka Python na platformie Azure](tutorial-1st-experiment-hello-world.md)
