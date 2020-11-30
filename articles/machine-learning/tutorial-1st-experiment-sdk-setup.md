---
title: 'Samouczek: Rozpoczynanie pracy w notesach Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Konfiguracja samouczków Jupyter Notebook. Tworzenie obszaru roboczego, klonowanie notesów w obszarze roboczym i tworzenie wystąpienia obliczeniowego, w którym są uruchamiane notesy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: ebde8048c26364d77d95b9c7b34f9d1d7a4a20ee
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326287"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Samouczek: wprowadzenie do Azure Machine Learning w notesach Jupyter

W tym samouczku wykonasz kroki umożliwiające rozpoczęcie pracy z usługą Azure Machine Learning przy użyciu notesów Jupyter na [zarządzanej stacji roboczej opartej na chmurze (wystąpienie obliczeniowe)](concept-compute-instance.md). Ten samouczek jest prekursorem do wszystkich innych samouczków Jupyter Notebook.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy Azure Machine Learning](concept-workspace.md) , który ma być używany w innych samouczkach Jupyter Notebook.
> * Sklonuj Notes samouczków do folderu w obszarze roboczym.
> * Tworzenie wystąpienia obliczeniowego opartego na chmurze z zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python Azure Machine Learning.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="create-a-workspace"></a>Utwórz obszar roboczy

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze.

Pomiń, aby [sklonować folder notesu](#clone) , jeśli masz już obszar roboczy Azure Machine Learning.  

Istnieje wiele [sposobów tworzenia obszaru roboczego](how-to-manage-workspace.md).  W tym samouczku utworzysz obszar roboczy za pośrednictwem Azure Portalej konsoli internetowej do zarządzania zasobami platformy Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Zanotuj swój *obszar roboczy* i *subskrypcję*. Te informacje będą potrzebne do utworzenia eksperymentu w odpowiednim miejscu.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Uruchamianie notesu w obszarze roboczym

Azure Machine Learning obejmuje serwer notesu chmury w obszarze roboczym na potrzeby instalacji bezpłatnej i wstępnie skonfigurowanego środowiska. Użyj [własnego środowiska](tutorial-1st-experiment-sdk-setup-local.md) , jeśli wolisz mieć kontrolę nad środowiskiem, pakietami i zależnościami.

 Postępuj zgodnie z tym filmem wideo lub wykonaj szczegółowe kroki w celu sklonowania i uruchomienia notesu samouczka z obszaru roboczego.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Klonowanie folderu notesu

Wykonaj następujące czynności konfiguracyjne i wykonaj kroki opisane w Azure Machine Learning Studio. Ten skonsolidowany interfejs zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych o wszystkich poziomach umiejętności.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Po lewej stronie wybierz pozycję **notesy**.

1. W górnej części Wybierz kartę **przykłady** .

1. Otwórz folder **Python** .

1. Otwórz w folderze numer wersji. Ta liczba reprezentuje bieżącą wersję zestawu Python SDK.

1. Wybierz przycisk **...** po prawej stronie folderu **samouczki** , a następnie wybierz pozycję **Klonuj**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Zrzut ekranu pokazujący folder klonowania samouczków.":::

1. Lista folderów przedstawia każdego użytkownika, który uzyskuje dostęp do obszaru roboczego. Wybierz folder, w którym ma zostać sklonowany folder **samouczków**  .

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Otwieranie sklonowanego notesu

1. Otwórz folder **samouczków** , który został zamknięty w sekcji **pliki użytkownika** .

    > [!IMPORTANT]
    > Notesy można wyświetlać w folderze **Samples** , ale nie można w tym miejscu uruchamiać notesu. Aby uruchomić Notes, upewnij się, że otwarto sklonowaną wersję notesu w sekcji **pliki użytkownika** .
    
1. Wybierz plik **IMG-klasyfikacyjn-part1-Train. ipynb** w folderze **samouczki/— Klasyfikacja obrazu-mnist ręcznie-Data** .

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Zrzut ekranu pokazujący folder otwartych samouczków.":::

1. Na górnym pasku wybierz wystąpienie obliczeniowe, które ma zostać użyte do uruchomienia notesu. Te maszyny wirtualne są wstępnie skonfigurowane z [wszystko, czego potrzebujesz do uruchamiania Azure Machine Learning](concept-compute-instance.md#contents).

1. Jeśli nie zostaną znalezione żadne maszyny wirtualne, wybierz pozycję **+ Dodaj** , aby utworzyć maszynę wirtualną wystąpienia obliczeniowego.

    1. Podczas tworzenia maszyny wirtualnej wykonaj następujące reguły:
 
        + Nazwa jest wymagana, a pole nie może być puste.
        + Nazwa musi być unikatowa (w sposób niezależny od wielkości liter) we wszystkich istniejących wystąpieniach obliczeniowych w regionie usługi Azure Workspace lub wystąpienia obliczeniowego. Otrzymasz alert, jeśli wybrana nazwa nie jest unikatowa.
        + Prawidłowe znaki to wielkie i małe litery, cyfry od 0 do 9 i znak kreski (-).
        + Nazwa musi mieć długość od 3 do 24 znaków.
        + Nazwa powinna zaczynać się literą, nie cyfrą ani znakiem kreski.
        + Jeśli jest używany znak kreski, musi następować co najmniej jedną literę po myślniku. Na przykład test-, test-0, test-01 jest nieprawidłowy, podczas gdy test-a0, test-0a są prawidłowymi wystąpieniami.

    1. Wybierz rozmiar maszyny wirtualnej z dostępnych opcji. Dla samouczków jest dobrym rozwiązaniem jest domyślna maszyna wirtualna.

    1. Następnie wybierz przycisk **Utwórz**. Skonfigurowanie maszyny wirtualnej może potrwać około pięciu minut.

1. Gdy maszyna wirtualna jest dostępna, zostanie wyświetlona na górnym pasku narzędzi. Teraz można uruchomić Notes przy użyciu polecenia **Uruchom wszystko** na pasku narzędzi lub **SHIFT + ENTER** w komórkach kodu notesu.

Jeśli masz niestandardowe widżety lub wolisz używać Jupyter lub JupyterLab, wybierz listę rozwijaną **Jupyter** po prawej stronie. Następnie wybierz pozycję **Jupyter** lub **JupyterLab**. Zostanie otwarte nowe okno przeglądarki.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już skonfigurowane środowisko programistyczne, Kontynuuj, aby nauczyć model w Jupyter Notebook.

> [!div class="nextstepaction"]
> [Samouczek: uczenie modeli klasyfikacji obrazów przy użyciu MNIST ręcznie danych i scikit — uczenie się](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Jeśli nie planujesz teraz następujących samouczków, Zatrzymaj maszynę wirtualną serwera notesu chmury, gdy nie używasz jej do obniżenia kosztów.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
