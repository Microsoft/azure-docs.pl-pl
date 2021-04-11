---
title: 'Samouczek: wprowadzenie do uczenia maszynowego — Python'
titleSuffix: Azure Machine Learning
description: Rozpocznij pracę z zestawem SDK Azure Machine Learning dla języka Python działającego w prywatnym środowisku programistycznym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: 69340245cdebda570b526afd4f11ac8314384b43
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210622"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Samouczek: wprowadzenie do Azure Machine Learning w środowisku programistycznym (część 1 z 4)

W tej *serii samouczków z czterema częściami* przedstawiono podstawy Azure Machine Learning i kończenia zadań uczenia maszynowego w języku Python na platformie Azure w chmurze. 

W części 1 tej serii samouczków będziesz:

> [!div class="checklist"]
> * Zainstaluj zestaw SDK Azure Machine Learning.
> * Skonfiguruj strukturę katalogów dla kodu.
> * Utwórz obszar roboczy Azure Machine Learning.
> * Skonfiguruj lokalne środowisko deweloperskie.
> * Skonfiguruj klaster obliczeniowy, czyli zasób oparty na chmurze do uczenia modeli.

Ta seria samouczków koncentruje się na Azure Machine Learning koncepcji wymaganych do przesłania **zadań wsadowych** — jest to miejsce, w którym kod jest przesyłany do chmury w celu uruchomienia w tle bez żadnej interakcji z użytkownikiem. Jest to przydatne w przypadku gotowych skryptów lub kodu, który ma być wykonywany wielokrotnie, lub do zadań uczenia maszynowego intensywnie korzystających z obliczeń.

Innym doskonałym sposobem na rozpoczęcie korzystania z Azure Machine Learning jest Jupyter notesy.  Zobacz [Samouczek: Rozpoczynanie pracy z Azure Machine Learning w notesach Jupyter](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Spróbuj [Azure Machine Learning](https://aka.ms/AMLFree).
- [Anaconda](https://www.anaconda.com/download/) lub [Miniconda](https://www.anaconda.com/download/) , aby zarządzać środowiskami wirtualnymi języka Python i zainstalować pakiety.  
- Jeśli nie masz doświadczenia w korzystaniu z usługi Conda, zobacz [wprowadzenie do usługi Conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html).
- Wszystkie środowiska IDE lub Edytor tekstu do tworzenia skryptów języka Python.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalowanie zestawu SDK Azure Machine Learning

W tym samouczku zostanie użyty zestaw Azure Machine Learning SDK dla języka Python. Aby uniknąć problemów z zależnościami w języku Python, utworzysz środowisko izolowane. Ta seria samouczków używa Conda do utworzenia tego środowiska. Jeśli wolisz używać innych rozwiązań, takich jak `venv` , `virtualenv` , lub platformy Docker, upewnij się, że używasz wersji Python >= 3,5 i < 3,9.

Sprawdź, czy w systemie jest zainstalowany program Conda:
    
```bash
conda --version
```
    
Jeśli to polecenie zwróci `conda not found` błąd, [Pobierz i zainstaluj Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

Po zainstalowaniu programu Conda należy użyć terminalu lub okna monitu Anaconda w celu utworzenia nowego środowiska:

```bash
conda create -n tutorial python=3.8
```

Następnie Zainstaluj zestaw SDK Azure Machine Learning w utworzonym środowisku Conda:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Ukończenie instalacji zestawu SDK Azure Machine Learning wymaga około 2 minut.
>
> Jeśli zostanie wyświetlony komunikat o błędzie limitu czasu, spróbuj `pip install --default-timeout=100 azureml-core` zamiast tego.


> [!div class="nextstepaction"]
> Po [zainstalowaniu zestawu SDK](?success=install-sdk#dir) [Wystąpił problem](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Tworzenie struktury katalogów dla kodu

Zalecamy skonfigurowanie następującej prostej struktury katalogów dla tego samouczka:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="Struktura katalogów: najwyższego poziomu samouczka z podkatalogiem.":::


- `tutorial`: Katalog najwyższego poziomu projektu.
- `.azureml`: Ukryty podkatalog do przechowywania plików konfiguracji Azure Machine Learning.

Na przykład, aby utworzyć ten element w oknie bash:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Aby utworzyć lub wyświetlić strukturę w oknie graficznym, należy najpierw włączyć możliwość wyświetlania i tworzenia ukrytych plików i folderów:
>
> * W oknie Wyszukiwacz Mac Użyj **polecenia + Shift +.** Aby włączyć wyświetlanie ukrytych plików/folderów.  
> * W Eksploratorze plików systemu Windows 10 zapoznaj [się z tematem jak wyświetlać ukryte pliki i foldery](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * W interfejsie graficznym systemu Linux Użyj **klawiszy CTRL + h** lub menu **Widok** , a następnie zaznacz pole, aby **wyświetlić ukryte pliki**.




> [!div class="nextstepaction"]
> [Utworzyłem katalog,](?success=create-dir#workspace) [w którym wystąpił problem](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Tworzenie obszaru roboczego usługi Azure Machine Learning

Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning i jest scentralizowanym miejscem do wykonania:

- Zarządzaj zasobami, takimi jak obliczenia.
- Przechowuj zasoby, takie jak notesy, środowiska, zestawy danych, potoki, modele i punkty końcowe.
- Współpracuj z innymi członkami zespołu.

W katalogu najwyższego poziomu `tutorial` Dodaj nowy plik Python o nazwie przy `01-create-workspace.py` użyciu następującego kodu. Dostosuj parametry (nazwa, Identyfikator subskrypcji, Grupa zasobów i [Lokalizacja](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) z preferencjami.

Kod można uruchomić w sesji interaktywnej lub w postaci pliku języka Python.

>[!NOTE]
> W przypadku korzystania z lokalnego środowiska programistycznego (na przykład komputera) użytkownik zostanie poproszony o uwierzytelnienie w obszarze roboczym przy użyciu *kodu urządzenia* podczas pierwszego uruchomienia poniższego kodu. Wykonaj instrukcje wyświetlane na ekranie.

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

W oknie, które ma aktywowane środowisko *tutorial1* Conda, Uruchom ten kod z `tutorial` katalogu.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Jeśli uruchomienie tego kodu spowoduje błąd, że nie masz dostępu do subskrypcji, zobacz [Tworzenie obszaru roboczego](how-to-manage-workspace.md?tab=python#create-multi-tenant) , aby uzyskać informacje na temat opcji uwierzytelniania.


Po pomyślnym uruchomieniu *01-Create-Workspace.py* struktura folderów będzie wyglądać następująco:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="Plik config.json pojawia się w katalogu. usługi Azure po uruchomieniu 01-create-workspace.py":::

Plik `.azureml/config.json` zawiera metadane niezbędne do nawiązania połączenia z obszarem roboczym Azure Machine Learning. A mianowicie zawiera identyfikator subskrypcji, grupę zasobów i nazwę obszaru roboczego. 

> [!NOTE]
> Zawartość `config.json` nie jest wpisami tajnymi. Warto udostępnić te szczegóły.
>
> Uwierzytelnianie jest nadal wymagane do współdziałania z obszarem roboczym Azure Machine Learning.

> [!div class="nextstepaction"]
> [Utworzono obszar roboczy,](?success=create-workspace#cluster) [w którym wystąpił problem](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Tworzenie klastra obliczeniowego Azure Machine Learning

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
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

W oknie, które ma aktywowane środowisko *tutorial1* Conda, uruchom plik języka Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Po utworzeniu klastra zostanie zainicjowana obsługa 0 węzłów. W klastrze *nie są* naliczane koszty, dopóki nie zostanie przesłane zadanie. Ten klaster zostanie przeskalowany w dół, gdy będzie bezczynny przez 2 400 sekund (40 minut).

Struktura folderów będzie teraz wyglądać następująco:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Dodawanie 02-create-compute.py do katalogu samouczków":::

> [!div class="nextstepaction"]
> [Klaster obliczeniowy został utworzony](?success=create-compute-cluster#next-steps) [w ramach problemu](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Wyświetl w Studio

Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com) , aby wyświetlić utworzone obszary robocze i wystąpienia obliczeniowe.

1. Wybierz **subskrypcję** , która została użyta do utworzenia obszaru roboczego.
1. Wybierz utworzony **obszar roboczy Machine Learning** , *samouczek — WS*.
1. Po załadowaniu obszaru roboczego po lewej stronie wybierz pozycję **Oblicz**.
1. W górnej części Wybierz kartę **Klastry obliczeniowe** .

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Zrzut ekranu: Wyświetl wystąpienie obliczeniowe w obszarze roboczym.":::

Ten widok przedstawia klaster obliczeniowy z obsługą administracyjną wraz z liczbą węzłów bezczynnych, zajętymi węzłami i nieudostępnianymi węzłami.  Ponieważ klaster nie został jeszcze użyty, wszystkie węzły są obecnie niezainicjowane.

## <a name="next-steps"></a>Następne kroki

W tym samouczku instalacji masz:

- Utworzono obszar roboczy Azure Machine Learning.
- Skonfiguruj lokalne środowisko programistyczne.
- Utworzono klaster obliczeniowy Azure Machine Learning.

W innych częściach tego samouczka znajdziesz następujące informacje:

* Część 2. Uruchom kod w chmurze za pomocą zestawu SDK Azure Machine Learning dla języka Python.
* Część 3. Zarządzaj środowiskiem Python używanym do szkolenia modeli.
* Część 4. Przekazuj dane na platformę Azure i korzystaj z nich w szkoleniu.

Przejdź do następnego samouczka, aby zapoznać się z przesyłaniem skryptu do Azure Machine Learning klastra obliczeniowego.

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie "Hello World!" Skrypt języka Python na platformie Azure](tutorial-1st-experiment-hello-world.md)
