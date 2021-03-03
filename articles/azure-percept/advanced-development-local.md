---
title: Zacznij korzystać z usługi Azure Percept Advanced Development lokalnie
description: Rozpocznij pracę z lokalnym programowaniem w usłudze Machine Learning przy użyciu VS Code i notesów Jupyter na platformie Azure
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664855"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Wprowadzenie do programowania w usłudze Machine Learning przy użyciu notesów VS Code i Jupyter na platformie Azure

W tym artykule omówiono proces konfigurowania obszaru roboczego Azure Machine Learning, tworzenia wystąpienia obliczeniowego, konfigurowania Visual Studio Code środowiska deweloperskiego na komputerze lokalnym oraz uruchamiania komórek wstępnie skonfigurowanego przykładowego notesu Jupyter w VS Code.

[Notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) wykonuje uczenie przenoszenia przy użyciu wstępnie przeszkolonego modelu TensorFlow (MobileNetSSDV2Lite) na platformie Azure w języku Python z niestandardowym zestawem danych w celu wykrywania wz.

W notesie pokazano, jak zacząć od [zestawu danych Coco](https://cocodataset.org/#home), odfiltrować go tylko do klasy zainteresowania (Puchary), a następnie przekonwertować ją na odpowiedni format. Alternatywnie można użyć narzędzia do etykietowania Open Source [VoTT 2](https://github.com/microsoft/VoTT) do tworzenia i etykietowania pól ograniczenia w formacie Pascala LZO.

Po pomyślnym przekształceniu modelu w niestandardowym zestawie danych model można wdrożyć na platformie Azure Percept DK przy użyciu metody aktualizacji z sznurem modułu. Następnie możesz sprawdzić model inferencing, wyświetlając strumień na żywo RTSP z elementu SoM usługi Azure Percept Vision. Zarówno ponowne uczenie modelu, jak i wdrożenie są wykonywane w notesie za pomocą zdalnego wystąpienia obliczeniowego.

## <a name="prerequisites"></a>Wymagania wstępne

- [Subskrypcja Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK z usługą Azure Percept Vision SoM połączone](./overview-azure-percept-dk.md)
- Zakończono [Korzystanie z usługi Azure PERCEPT DK na płycie](./quickstart-percept-dk-set-up.md)

## <a name="download-azure-percept-github-repository"></a>Pobierz repozytorium GitHub usługi Azure Percept

1. Przejdź do [repozytorium GitHub usługi Azure Percept](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Sklonuj repozytorium lub Pobierz plik ZIP. W górnej części ekranu kliknij pozycję **kod**  ->  **Pobierz plik zip**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="Ekran pobierania usługi GitHub.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Tworzenie obszaru roboczego Azure Machine Learning i zdalnego wystąpienia obliczeniowego

1. Przejdź do [portalu Azure Machine Learning](https://ml.azure.com).
1. Wybierz katalog, subskrypcję platformy Azure i obszar roboczy Machine Learning z menu rozwijanego i kliknij pozycję **Rozpocznij**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Ekran Wprowadzenie do usługi Azure ML.":::

    Jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning, kliknij przycisk **Utwórz nowy obszar roboczy**. Na nowej karcie przeglądarki wykonaj następujące czynności:

    1. Wybierz swoją subskrypcję platformy Azure.
    1. Wybierz swoją grupę zasobów.
    1. Wprowadź nazwę obszaru roboczego.
    1. Wybierz region.
    1. Wybierz wersję swojego obszaru roboczego.
    1. Kliknij pozycję **Przejrzyj i utwórz**.
    1. Na następnej stronie Przejrzyj wybrane opcje i kliknij przycisk **Utwórz**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Ekran tworzenia obszaru roboczego w usłudze Azure ML.":::

    Zaczekaj kilka minut na utworzenie obszaru roboczego. Po zakończeniu tworzenia obszaru roboczego zobaczysz zielony znacznik wyboru obok zasobów, a **wdrożenie zostanie ukończone** w górnej części strony Przegląd Machine Learning Services.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Potwierdzenie utworzenia obszaru roboczego." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Po zakończeniu tworzenia obszaru roboczego Wróć do karty Portal uczenia maszynowego i kliknij pozycję **Rozpocznij pracę**.

1. Na stronie głównej obszaru roboczego uczenia maszynowego kliknij pozycję **obliczenia** w okienku po lewej stronie.

1. Jeśli nie ma istniejącego wystąpienia obliczeniowego, Utwórz nowe obciążenie procesora CPU lub procesora GPU, klikając pozycję **nowe**. W oknie **nowe wystąpienie obliczeniowe** wprowadź **nazwę obliczenia**, wybierz **Typ maszyny wirtualnej**, a następnie wybierz **rozmiar maszyny wirtualnej**. Kliknij pozycję **Utwórz**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Nowy ekran tworzenia wystąpienia obliczeniowego.":::

    Po kliknięciu przycisku **Utwórz** utworzenie wystąpienia obliczeniowego trwa kilka minut. Stan **obliczeń** będzie wyświetlać zielony okrąg i **\<your compute name> -działa** po zakończeniu tworzenia obliczeń. To wystąpienie obliczeniowe uruchamia serwer Jupyter i zostanie użyte w tym samouczku.

## <a name="visual-studio-code-development-environment-setup"></a>Konfiguracja środowiska deweloperskiego Visual Studio Code

1. Zainstaluj wymagane narzędzia.

    1. Opcja 1.

        Użyj [Instalatora pakietu narzędzi deweloperskich](./dev-tools-installer.md) , aby zainstalować następujące pakiety:

        - Visual Studio Code
        - Python 3,5, 3,6 lub 3,7
        - Miniconda3
        - Zestaw narzędzi Intel OpenVino 2020,2

        Uwaga: zestaw narzędzi Intel OpenVino Toolkit jest wymieniony jako opcjonalny pakiet w Instalatorze pakietu narzędzi deweloperskich, ale jest wymagany do pracy z modelem Som "Azure Percept Vision" w ramach zestawu Azure Percept DK Development Kit.

    1. Opcja 2.

        Jeśli wolisz nie korzystać z Instalatora pakietu narzędzi deweloperskich, ręcznie Zainstaluj następujące pakiety, klikając poniższe linki i postępując zgodnie z określonymi wskazówkami dotyczącymi pobierania i instalacji:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3,5, 3,6 lub 3,7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Zestaw narzędzi Intel OpenVino 2020,2](https://docs.openvinotoolkit.org/)

    Uwaga: Jeśli masz już zainstalowaną pełną dystrybucję Anaconda, nie musisz instalować Miniconda. Alternatywnie, jeśli wolisz nie używać Anaconda lub Miniconda, możesz utworzyć środowisko wirtualne języka Python i zainstalować pakiety wymagane do uruchamiania tworzenia modelu AI przy użyciu narzędzia PIP.

1. Uruchom program Visual Studio Code.
1. Skonfiguruj środowisko nauki o danych:

    - Opcja 1 — nawiązywanie połączenia z istniejącym lub nowym wystąpieniem obliczeniowym zdalnego uczenia maszynowego, które ma już nadzorowane pakiety ML. **Jest to opcja, którą będziemy używać w tym samouczku**.

    - Opcja 2 — Konfigurowanie środowiska Conda na komputerze lokalnym.
        1. Otwórz wiersz polecenia Anaconda lub Miniconda i uruchom następujące polecenie, aby utworzyć środowisko o nazwie **MyENV** z określonymi pakietami:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Aby uzyskać dodatkowe informacje na temat tworzenia środowisk Anaconda i zarządzania nimi, zobacz [dokumentację Anaconda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. Utwórz obszar roboczy VS Code:

    1. Utwórz folder w wygodnej lokalizacji, który będzie używany jako obszar roboczy Visual Studio Code. Nadaj mu nazwę **webworkspace**.
    1. Otwórz **obszar moje obszary robocze** w Visual Studio Code, klikając pozycję **plik**  >  **Otwórz folder**  >  **Wybierz folder**.
    1. W Eksploratorze plików przejdź do i wybierz [plik Transferlearningusing_SSDLiteV2 model. ipynbb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) z lokalnej kopii repozytorium GitHub usługi Azure Percept. Skopiuj ten plik notesu do folderu obszaru roboczego.

## <a name="azure-integration-with-visual-studio-code"></a>Integracja z platformą Azure z usługą Visual Studio Code

1. Jeśli jeszcze tego nie zrobiono, zarejestruj się w celu uzyskania [bezpłatnej lub płatnej wersji Azure Machine Learning](https://aka.ms/AMLFree).

1. Zainstaluj następujące rozszerzenia platformy Azure dla VS Code:
    - [Azure Machine Learning rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Rozszerzenie dla niejawnych testerów języka Python. W vs Code przejdź do **widoku**  ->  **paleta poleceń**. W palecie poleceń wprowadź i wybierz język **Python: Przełącz na kanał dzienny dla niejawnych kanałów**.
    - [Rozszerzenie konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Załaduj ponownie okno w VS Code po wyświetleniu monitu.
    - [Rozszerzenie zestawu narzędzi platformy Azure IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Azure IoT Edge rozszerzenie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Zaloguj się do konta platformy Azure w Visual Studio Code, aby udostępnić zasoby i uruchamiać obciążenia na platformie Azure.
    1. Otwórz paletę poleceń w Visual Studio Code, wybierając pozycję **Widok**  >  **paleta poleceń** na pasku menu.
    1. Wprowadź **Azure: Zaloguj się** do palety poleceń, aby rozpocząć proces logowania.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Ekran logowania na platformie Azure." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Aktywuj rozszerzenie Python i konto platformy Azure, klikając ikonę platformy Azure po lewej stronie VS Code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Ikona platformy Azure w VS Code.":::

    1. Otwórz Notes Transferlearningusing_SSDLiteV2 Model_VSCode. ipynb z folderu **obszaru roboczego** .
    1. Otwórz paletę poleceń. Wprowadź i wybierz język **Python: Określ lokalny lub zdalny serwer Jupyter dla połączeń**.
    1. Powinna zostać wyświetlona lista opcji połączenia do wyboru. Wybierz **wystąpienia usługi Azure środowisko obliczeniowe usługi ml**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Opcje wystąpienia obliczeniowego usługi Azure ML w VS Code.":::

    1. Postępuj zgodnie z instrukcjami z przewodnikiem, aby wybrać subskrypcję, obszar roboczy i wystąpienie obliczeniowe zdalne. Wybierz obszar roboczy i zdalne wystąpienie obliczeniowe utworzone wcześniej w tym samouczku. Istnieje również możliwość utworzenia nowego wystąpienia obliczeniowego.
    1. Po wybraniu wystąpienia obliczeniowego zostanie wyświetlony monit o ponowne załadowanie okna VS Code. Po ponownym załadowaniu wybierz jądro **Python 3,6-Azure** . Teraz można uruchamiać dowolną komórkę w notesie. Uruchomienie komórki notesu spowoduje utworzenie wystąpienia połączenia między notesem a wystąpieniem obliczeniowym. Pasek narzędzi notesu zostanie zaktualizowany w celu odzwierciedlenia wystąpienia obliczeniowego, nad którym pracujesz.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Wybór jądra w VS Code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Praca z notesem

Teraz można przystąpić do uruchamiania notesu, aby szkolić niestandardowy detektor w VS Code i wdrożyć go w Devkit. Upewnij się, że każda komórka notesu jest uruchamiana pojedynczo, ponieważ niektóre komórki wymagają parametrów wejściowych przed wykonaniem skryptu. Komórki wymagające parametrów wejściowych mogą być edytowane bezpośrednio w notesie. Aby uruchomić komórkę, kliknij ikonę odtwarzania po lewej stronie komórki:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Ikona komórki wyróżniania notesu.":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe przykładowe notesy usługi Azure Machine Learning, odwiedź to [repozytorium](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
