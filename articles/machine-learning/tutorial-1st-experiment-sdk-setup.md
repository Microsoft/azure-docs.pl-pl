---
title: 'Samouczek: Rozpoczynanie pracy w notesach Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Konfiguracja samouczków Jupyter Notebook.  Utwórz obszar roboczy Azure Machine Learning, Sklonuj notesy Jupyter w obszarze roboczym i Utwórz wystąpienie obliczeniowe, w którym są uruchamiane notesy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896891"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Samouczek: wprowadzenie do Azure Machine Learning w notesach Jupyter

W tym samouczku wykonasz kroki umożliwiające rozpoczęcie pracy z Azure Machine Learning przy użyciu notesów Jupyter na [zarządzanej stacji roboczej opartej na chmurze (wystąpienie obliczeniowe)](concept-compute-instance.md). Ten samouczek jest prekursorem do wszystkich innych samouczków Jupyter Notebook.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy usługi Azure Machine Learning](concept-workspace.md) do użycia w innych samouczkach Jupyter Notebook.
> * Sklonuj Notes samouczków do folderu w obszarze roboczym.
> * Tworzenie wystąpienia obliczeniowego opartego na chmurze z zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python Azure Machine Learning.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze.

Aby zarządzać zasobami platformy Azure, można utworzyć obszar roboczy za pośrednictwem Azure Portal konsoli internetowej.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Uruchamianie notesu w obszarze roboczym

Azure Machine Learning obejmuje serwer notesu w chmurze w obszarze roboczym na potrzeby instalacji i wstępnie skonfigurowanego środowiska. Jeśli wolisz sterować środowiskiem, pakietami i zależnościami, użyj [własnego środowiska](tutorial-1st-experiment-sdk-setup-local.md) .

 Postępuj zgodnie z tym wideo lub wykonaj poniższe szczegółowe czynności, aby sklonować i uruchamiać Notes samouczka z obszaru roboczego.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Klonowanie folderu notesu

Należy wykonać następujące czynności w celu skonfigurowania i uruchomienia kroków w programie Azure Machine Learning Studio — skonsolidowany interfejs, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych wszystkich poziomów umiejętności.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz **notesy** po lewej stronie.

1. Wybierz kartę **przykłady** w górnej części ekranu.

1. Otwórz folder **Python** .

1. Otwórz w folderze numer wersji.  Ta liczba reprezentuje bieżącą wersję zestawu Python SDK.

1. Wybierz pozycję **"..."** po prawej stronie folderu **samouczki** , a następnie wybierz pozycję **Klonuj**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Folder klonów samouczków":::

1. Zostanie wyświetlona lista folderów pokazująca każdego użytkownika, który uzyskuje dostęp do obszaru roboczego.  Wybierz folder, w którym ma zostać sklonowany folder **samouczków**  .

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Otwieranie sklonowanego notesu

1. Otwórz folder **samouczków** , który został właśnie zamknięty w sekcji **plików użytkownika** .

    > [!IMPORTANT]
    > Notesy można wyświetlać w folderze **Samples** , ale nie można w tym miejscu uruchamiać notesu.  W celu uruchomienia notesu upewnij się, że otwarto sklonowaną wersję notesu w sekcji **pliki użytkownika** .
    
1. Wybierz plik **samouczek — pierwszy eksperyment-zestaw SDK-pociąg. ipynb** w folderze **samouczki/obraz-Klasyfikacja-mnist ręcznie-dane** .

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Otwórz folder samouczków":::

1. Na górnym pasku wybierz wystąpienie obliczeniowe, które ma zostać użyte do uruchomienia notesu. Te maszyny wirtualne są wstępnie skonfigurowane z [wszystko, czego potrzebujesz do uruchamiania Azure Machine Learning](concept-compute-instance.md#contents).

1. Jeśli nie zostaną znalezione żadne maszyny wirtualne, wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną wystąpienia obliczeniowego. 

    1. Podczas tworzenia maszyny wirtualnej wykonaj następujące reguły:  
        + Nazwa jest wymagana i nie może być pusta.
        + Nazwa musi być unikatowa (w sposób niezależny) we wszystkich istniejących wystąpieniach obliczeniowych w regionie platformy Azure w obszarze roboczym/wystąpieniu obliczeniowym. Otrzymasz alert, jeśli wybrana nazwa nie jest unikatowa.
        + Prawidłowe znaki to wielkie i małe litery, cyfry (od 0 do 9) i znak kreski (-).
        + Nazwa musi mieć długość od 3 do 24 znaków.
        + Nazwa powinna zaczynać się literą (nie cyfrą ani znakiem kreski).
        + Jeśli jest używany znak kreski, należy po nim następować co najmniej jedną literę. Przykład: test-, test-0, test-01 jest nieprawidłowy, podczas gdy test-a0, test-0a są prawidłowymi wystąpieniami.

    1.  Wybierz rozmiar maszyny wirtualnej z dostępnych opcji. Dla samouczków jest dobrym rozwiązaniem jest domyślna maszyna wirtualna.

    1. Następnie wybierz przycisk **Utwórz**. Skonfigurowanie maszyny wirtualnej może potrwać około 5 minut.

1. Gdy maszyna wirtualna będzie dostępna, zostanie wyświetlona na górnym pasku narzędzi.  Można teraz uruchomić Notes przy użyciu opcji **Uruchom wszystko** na pasku narzędzi lub naciskając **klawisze SHIFT + ENTER** w komórkach kodu notesu.

Jeśli masz niestandardowe widżety lub Preferuj przy użyciu Jupyter/JupyterLab wybierz listę rozwijaną **Jupyter** po prawej stronie, a następnie wybierz pozycję **Jupyter** lub **JupyterLab**. Zostanie otwarte nowe okno przeglądarki.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już skonfigurowane środowisko programistyczne, Kontynuuj, aby nauczyć model w Jupyter Notebook:

> [!div class="nextstepaction"]
> [Samouczek: uczenie modeli klasyfikacji obrazów przy użyciu MNIST ręcznie danych i scikit — uczenie się](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Jeśli nie planujesz teraz wykonywania żadnych innych samouczków, Zatrzymaj maszynę wirtualną serwera notesu chmury, gdy nie używasz jej do obniżenia kosztów:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
