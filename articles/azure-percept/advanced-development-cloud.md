---
title: Wprowadzenie do zaawansowanego programowania na platformie Azure Percept w chmurze
description: Rozpocznij pracę z zaawansowanym programowaniem w chmurze za pośrednictwem notesów Jupyter i Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664800"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Wprowadzenie do zaawansowanego programowania w chmurze za pośrednictwem notesów Jupyter i Azure Machine Learning

Ten artykuł przeprowadzi Cię przez proces konfigurowania obszaru roboczego Azure Machine Learning, przekazywania wstępnie skonfigurowanego przykładowego Jupyter Notebook do obszaru roboczego, tworzenia wystąpienia obliczeniowego i uruchamiania komórek notesu w obszarze roboczym.

[Notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) wykonuje uczenie przenoszenia przy użyciu wstępnie przeszkolonego modelu TensorFlow (MobileNetSSDV2Lite) na platformie Azure w języku Python z niestandardowym zestawem danych w celu wykrywania wz.

W notesie pokazano, jak rozpocząć od [Coco](https://cocodataset.org/#home), odfiltrować ją tylko do klasy zainteresowania (Puchary), a następnie przekonwertować ją na odpowiedni format. Alternatywnie można użyć narzędzia do etykietowania Open Source [VoTT 2](https://github.com/microsoft/VoTT) do tworzenia i etykietowania pól ograniczenia w formacie Pascala LZO.

Po pomyślnym przekształceniu modelu w niestandardowym zestawie danych model można następnie wdrożyć na platformie Azure Percept DK przy użyciu metody aktualizacji z zastosowaniem sieci.

Następnie możesz sprawdzić model inferencing, wyświetlając strumień na żywo RTSP z elementu SoM usługi Azure Percept Vision. Zarówno ponowne uczenie modelu, jak i wdrożenie są wykonywane w ramach notesu w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

- [Subskrypcja Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK z usługą Azure Percept Vision SoM połączone](./overview-azure-percept-dk.md)
- Zakończono [Korzystanie z usługi Azure PERCEPT DK na płycie](./quickstart-percept-dk-set-up.md)

## <a name="download-azure-percept-github-repository"></a>Pobierz repozytorium GitHub usługi Azure Percept

1. Przejdź do [repozytorium GitHub usługi Azure Percept](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Sklonuj repozytorium lub Pobierz plik ZIP. W górnej części ekranu kliknij pozycję **kod**  ->  **Pobierz plik zip**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="Ekran pobierania usługi GitHub.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Konfigurowanie portalu Azure Machine Learning i notesu

1. Przejdź do [portalu Azure Machine Learning](https://ml.azure.com).

1. Wybierz katalog, subskrypcję platformy Azure i obszar roboczy Machine Learning z menu rozwijanego i kliknij pozycję **Rozpocznij**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Ekran Wprowadzenie do usługi Azure ML.":::

    Jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning, kliknij przycisk **Utwórz nowy obszar roboczy**. Na nowej karcie przeglądarki wykonaj następujące czynności:

    1. Wybierz swoją subskrypcję platformy Azure.
    1. Wybierz swoją grupę zasobów.
    1. Wprowadź nazwę obszaru roboczego.
    1. Wybierz region.
    1. Wybierz wersję swojego obszaru roboczego.
    1. Kliknij pozycję **Przejrzyj i utwórz**.
    1. Na następnej stronie Przejrzyj wybrane opcje i kliknij przycisk **Utwórz**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Ekran tworzenia obszaru roboczego w usłudze Azure ML.":::

    Zaczekaj kilka minut na utworzenie obszaru roboczego. Po zakończeniu tworzenia obszaru roboczego zobaczysz zielony znacznik wyboru obok zasobów, a **wdrożenie zostanie ukończone** w górnej części strony Przegląd Machine Learning Services.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Potwierdzenie utworzenia obszaru roboczego." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Po zakończeniu tworzenia obszaru roboczego Wróć do karty Portal uczenia maszynowego i kliknij pozycję **Rozpocznij pracę**.

1. Na stronie głównej obszaru roboczego uczenia maszynowego kliknij pozycję **notesy** w okienku po lewej stronie.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Strona główna platformy Azure ML.":::

1. Na karcie **Moje pliki** kliknij strzałkę w pionie, aby przekazać plik. ipynb.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Strona główna wyróżniająca ikonę Przekaż plik.":::

1. Przejdź do i wybierz [plik Transferlearningusing_SSDLiteV2 model. ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) z lokalnej kopii repozytorium GitHub usługi Azure Percept. Kliknij przycisk **Otwórz**. W oknie **przekazywanie plików** zaznacz pole wyboru obok pozycji **ufaj zawartości z tego pliku** i kliknij przycisk **Przekaż**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Ekran wyboru pliku.":::

1. W prawym górnym pasku menu Sprawdź stan **obliczeń** . Jeśli nie zostaną znalezione żadne obliczenia, kliknij **+** ikonę, aby utworzyć nowe obliczenie.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Stan obliczeń z wyróżnioną ikoną +." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. W oknie **nowe wystąpienie obliczeniowe** wprowadź **nazwę obliczenia**, wybierz **Typ maszyny wirtualnej**, a następnie wybierz **rozmiar maszyny wirtualnej**. Kliknij pozycję **Utwórz**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Nowy ekran tworzenia wystąpienia obliczeniowego.":::

    Po kliknięciu przycisku **Utwórz** stan **obliczeń** będzie wyświetlał niebieskie koło i **\<your compute name> -Tworzenie**

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Stan obliczeń podczas tworzenia obliczeń jest nadal w toku.":::

    Stan **obliczeń** będzie wyświetlać zielony okrąg i **\<your compute name> -działa** po zakończeniu tworzenia obliczeń.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="Stan obliczeń przedstawiający tworzenie obliczeń zostało zakończone.":::

1. Po uruchomieniu obliczeń wybierz jądro **Python 3,6-Azure** z menu rozwijanego obok **+** ikony.

## <a name="working-with-the-notebook"></a>Praca z notesem

Teraz można przystąpić do uruchamiania notesu w celu uczenia niestandardowego detektora pucharowego i wdrożenia go w Devkit. Upewnij się, że każda komórka notesu jest uruchamiana pojedynczo, ponieważ niektóre komórki wymagają parametrów wejściowych przed wykonaniem skryptu. Komórki wymagające parametrów wejściowych mogą być edytowane bezpośrednio w notesie. Aby uruchomić komórkę, kliknij ikonę odtwarzania po lewej stronie komórki:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Ikona komórki wyróżniania notesu." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe przykładowe notesy usługi Azure Machine Learning Service, odwiedź to [repozytorium](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml) .
