---
title: Jak uzyskać dostęp do terminalu wystąpienia obliczeniowego w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Użyj terminalu w wystąpieniu obliczeniowym dla operacji git, aby zainstalować pakiety i dodać jądra.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101565"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Dostęp do terminalu wystąpienia obliczeniowego w obszarze roboczym

Uzyskaj dostęp do terminalu wystąpienia obliczeniowego w obszarze roboczym, aby:

* Używaj plików z usługi git i plików wersji. Te pliki są przechowywane w systemie plików obszaru roboczego, a nie ograniczone do pojedynczego wystąpienia obliczeniowego.
* Zainstaluj pakiety w wystąpieniu obliczeniowym.
* Utwórz dodatkowe jądra w wystąpieniu obliczeniowym.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Dostęp do terminalu

Aby uzyskać dostęp do terminalu:

1. Otwórz obszar roboczy w programie [Azure Machine Learning Studio](https://ml.azure.com).
1. Po lewej stronie wybierz pozycję **notesy**.
1. Wybierz pozycję **Otwórz obraz terminalu** .

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Otwórz okno terminalu":::

1. Po uruchomieniu wystąpienia obliczeniowego zostanie wyświetlone okno terminalu dla tego wystąpienia obliczeniowego.
1. Gdy żadne wystąpienie obliczeniowe nie jest uruchomione, użyj sekcji **COMPUTE (obliczenia** ) po prawej stronie, aby uruchomić lub utworzyć wystąpienie obliczeniowe.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Uruchamianie lub tworzenie wystąpienia obliczeniowego":::

Oprócz powyższych kroków można również uzyskać dostęp do terminalu z:

* RStudio: Wybierz kartę **terminalu** u góry po lewej stronie.
* Jupyter Lab: wybierz kafelek **terminalu** pod **innym** nagłówkiem na karcie Uruchamianie.
* Jupyter: wybierz pozycję **Nowy Terminal>** w prawym górnym rogu na karcie pliki.
* Protokół SSH do maszyny, jeśli włączono dostęp SSH podczas tworzenia wystąpienia obliczeniowego.

## <a name="copy-and-paste-in-the-terminal"></a>Kopiowanie i wklejanie w terminalu

> * Windows: `Ctrl-Insert` do kopiowania i używania `Ctrl-Shift-v` lub `Shift-Insert` do wklejania.
> * Mac OS: `Cmd-c` kopiowanie i `Cmd-v` wklejanie.
> * Program FireFox/IE może prawidłowo obsługiwać uprawnienia do Schowka.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Używanie plików z usługi git i plików wersji

Uzyskaj dostęp do wszystkich operacji git z terminalu. Wszystkie pliki i foldery git będą przechowywane w systemie plików obszaru roboczego. Ten magazyn umożliwia korzystanie z tych plików z dowolnego wystąpienia obliczeniowego w obszarze roboczym.

> [!NOTE]
> Dodaj pliki i foldery w dowolnym miejscu w folderze **~/CloudFiles/Code/users** , aby były widoczne we wszystkich środowiskach Jupyter.

Dowiedz się więcej [na temat klonowania repozytoriów Git w systemie plików obszaru roboczego](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Zainstaluj pakiety

 Zainstaluj pakiety z okna terminalu. Zainstaluj pakiety języka Python w środowisku **python 3,6-Azure** .  Zainstaluj pakiety języka R w środowisku **języka r** .

Lub można instalować pakiety bezpośrednio w Jupyter Notebook lub RStudio:

* RStudio Użyj karty **pakiety** w prawym dolnym rogu lub karty **konsoli** w lewym górnym rogu.  
* Python: Dodawanie kodu instalacji i wykonywanie w komórce Jupyter Notebook.

> [!NOTE]
> Aby zarządzać pakietami w ramach notesu, użyj funkcji **% PIP** lub **% Conda** Magic w celu automatycznego zainstalowania pakietów w **aktualnie uruchomionym jądrze** zamiast **! PIP** lub **! Conda** , które odwołują się do wszystkich pakietów (w tym pakietów poza aktualnie uruchomionym jądrem).

## <a name="add-new-kernels"></a>Dodaj nowe jądra

> [!WARNING]
>  Podczas dostosowywania wystąpienia obliczeniowego upewnij się, że nie usuniesz środowiska **azureml_py36** Conda ani jądra **Python 3,6-Azure** . Jest to niezbędny w przypadku funkcji Jupyter/JupyterLab

Aby dodać nowe jądro Jupyter do wystąpienia obliczeniowego:

1. Użyj okna terminalu, aby utworzyć nowe środowisko.  Na przykład poniższy kod tworzy `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Aktywuj środowisko.  Na przykład po utworzeniu `newenv` :

    ```shell
    conda activate newenv
    ```

1. Zainstaluj pakiet PIP i ipykernel w nowym środowisku i Utwórz jądro dla tej Conda ENV

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Można zainstalować dowolne z [dostępnych jądra Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

## <a name="manage-terminal-sessions"></a>Zarządzanie sesjami terminalu

 Wybierz pozycję **Wyświetl aktywne sesje** na pasku narzędzi terminalu, aby wyświetlić listę wszystkich aktywnych sesji terminalu. Gdy nie ma aktywnych sesji, ta karta zostanie wyłączona.

Zamknij wszystkie nieużywane sesje, aby zachować zasoby wystąpienia obliczeniowego.