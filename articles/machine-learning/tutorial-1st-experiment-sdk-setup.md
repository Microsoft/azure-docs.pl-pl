---
title: 'Samouczek: Tworzenie pierwszego eksperymentu z ML'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nastąpi rozpoczęcie pracy z zestawem SDK środowiska Azure Machine Learning Python uruchomionym w notesach Jupyter.  W części 1 utworzysz obszar roboczy, w którym będziesz zarządzać eksperymentami i modelami ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 3177de6816dd690514620098e79db844077fbaf6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655446"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Samouczek: wprowadzenie do tworzenia pierwszego eksperymentu z użyciem zestawu SDK języka Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku przedstawiono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter. Ten samouczek jest **częścią jednej z serii samouczków z dwiema częściami**oraz obejmuje instalację i konfigurację środowiska Python oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego. [**Częściowo dwie**](tutorial-1st-experiment-sdk-train.md) kompilacje na ten temat umożliwiają uczenie wielu modeli uczenia maszynowego i wprowadzenie procesu zarządzania modelami przy użyciu zarówno Azure Machine Learning Studio, jak i zestawu SDK.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy usługi Azure Machine Learning](concept-workspace.md) do użycia w następnym samouczku.
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

W tym samouczku używany jest serwer notesu chmury w obszarze roboczym do obsługi wstępnie skonfigurowanego środowiska instalacji. Jeśli wolisz sterować środowiskiem, pakietami i zależnościami, użyj [własnego środowiska](how-to-configure-environment.md#local) .

 Postępuj zgodnie z tym filmem wideo lub użyj szczegółowych kroków poniżej, aby sklonować i uruchomić samouczek z obszaru roboczego. 

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

1. Zostanie wyświetlona lista folderów pokazująca każdego użytkownika, który uzyskuje dostęp do obszaru roboczego.  Wybierz folder, w którym ma zostać sklonowany folder **samouczków** .

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Otwieranie sklonowanego notesu

1. Otwórz folder **samouczków** , który został właśnie zamknięty w sekcji **plików użytkownika** .

    > [!IMPORTANT]
    > Notesy można wyświetlać w folderze **Samples** , ale nie można w tym miejscu uruchamiać notesu.  W celu uruchomienia notesu upewnij się, że otwarto sklonowaną wersję notesu w sekcji **pliki użytkownika** .
    
1. Wybierz **samouczek — pierwszy eksperyment z zestawem SDK — uczenie. ipynb** w folderze **samouczki/Utwórz pierwszy-ml eksperymentu** .

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

    1.  Wybierz rozmiar maszyny wirtualnej z dostępnych opcji.

    1. Następnie wybierz pozycję **Utwórz**. Skonfigurowanie maszyny wirtualnej może potrwać około 5 minut.

1. Gdy maszyna wirtualna będzie dostępna, zostanie wyświetlona na górnym pasku narzędzi.  Można teraz uruchomić Notes przy użyciu opcji **Uruchom wszystko** na pasku narzędzi lub naciskając **klawisze SHIFT + ENTER** w komórkach kodu notesu.

Jeśli masz niestandardowe widżety lub Preferuj przy użyciu Jupyter/JupyterLab wybierz listę rozwijaną **Jupyter** po prawej stronie, a następnie wybierz pozycję **Jupyter** lub **JupyterLab**. Zostanie otwarte nowe okno przeglądarki.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

* Utworzono obszar roboczy Azure Machine Learning.
* Utworzono i skonfigurowano serwer notesu w chmurze w obszarze roboczym.

W **drugiej części** samouczka uruchomiono kod w `tutorial-1st-experiment-sdk-train.ipynb` celu uczenia modelu uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Samouczek: uczenie swojego pierwszego modelu](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Jeśli nie planujesz wykonywania następujących czynności w ramach tego samouczka lub innych samouczków, [Zatrzymaj maszynę wirtualną serwera notesu chmury](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , gdy nie używasz jej do obniżenia kosztów.


