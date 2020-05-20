---
title: Jak uruchamiać notesy Jupyter w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać Jupyter Notebook bez opuszczania obszaru roboczego w programie Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 96e37afd8bf7d59eef4a4c0c831f535faa36d34d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681443"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Jak uruchamiać notesy Jupyter w obszarze roboczym
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak uruchamiać notesy Jupyter bezpośrednio w obszarze roboczym w programie Azure Machine Learning Studio. Chociaż możesz uruchomić [Jupyter](https://jupyter.org/) lub [JupyterLab](https://jupyterlab.readthedocs.io), możesz również edytować i uruchamiać notesy bez opuszczania obszaru roboczego.

Zobacz, jak to zrobić:

* Tworzenie notesów Jupyter w obszarze roboczym
* Uruchamianie eksperymentu z notesu
* Zmień środowisko notesu
* Znajdowanie szczegółowych informacji o wystąpieniach obliczeniowych używanych do uruchamiania notesów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Tworzenie notesów

W obszarze roboczym Azure Machine Learning Utwórz nowy Notes Jupyter i Rozpocznij pracę. Nowo utworzony Notes jest przechowywany w domyślnym magazynie obszarów roboczych. Ten Notes może być udostępniany wszystkim użytkownikom mającym dostęp do obszaru roboczego. 

Aby utworzyć nowy Notes: 

1. Otwórz obszar roboczy w programie [Azure Machine Learning Studio](https://ml.azure.com).
1. Po lewej stronie wybierz pozycję **notesy**. 
1. Wybierz ikonę **Utwórz nowy plik** nad listą **plików użytkownika** w sekcji **Moje pliki** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Utwórz nowy plik":::

1. Nazwij plik. 
1. Dla plików Jupyter Notebook wybierz pozycję **Notes Python** jako typ pliku.
1. Wybierz katalog plików.
1. Wybierz przycisk **Utwórz**.

> [!TIP]
> Można również tworzyć pliki tekstowe.  Zaznacz **tekst** jako typ pliku i Dodaj rozszerzenie do nazwy (na przykład MyFile.py lub plik txt)  

Możesz również przekazać foldery i pliki, w tym notesy, za pomocą narzędzi w górnej części strony notesy.  Notesy i większość typów plików tekstowych są wyświetlane w sekcji Podgląd.  Wersja zapoznawcza nie jest dostępna dla większości innych typów plików.

### <a name="clone-samples"></a>Klonowanie próbek

Obszar roboczy zawiera folder **Samples** z notesami, które ułatwiają zapoznanie się z zestawem SDK i służą jako przykłady dla własnych projektów uczenia maszynowego.  Można sklonować te notesy do własnego folderu w kontenerze magazynu obszaru roboczego.  

Aby zapoznać się z przykładem, zobacz [Samouczek: Tworzenie pierwszego eksperymentu z](tutorial-1st-experiment-sdk-setup.md#azure)tablicą.

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a>Używaj plików z narzędzia Git i wersji plików my

Dostęp do wszystkich operacji git można uzyskać przy użyciu okna terminalu. Wszystkie pliki i foldery git będą przechowywane w systemie plików obszaru roboczego.

> [!NOTE]
> Dodaj pliki i foldery w dowolnym miejscu w folderze **~/CloudFiles/Code/users** , aby były widoczne we wszystkich środowiskach Jupyter.

Aby uzyskać dostęp do terminalu:

1. Otwórz obszar roboczy w programie [Azure Machine Learning Studio](https://ml.azure.com).
1. Po lewej stronie wybierz pozycję **notesy**.
1. Wybierz dowolny Notes znajdujący się w sekcji **pliki użytkownika** po lewej stronie.  Jeśli nie masz tam żadnych notesów, najpierw [Utwórz Notes](#create)
1. Wybierz element docelowy **obliczeń** lub Utwórz nowy i poczekaj na jego uruchomienie.
1. Wybierz ikonę **Otwórz Terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Otwórz Terminal":::

1. Jeśli nie widzisz ikony, wybierz pozycję **...** z prawej strony elementu docelowego obliczeń, a następnie wybierz pozycję **Otwórz Terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Otwórz Terminal z...":::


Dowiedz się więcej [na temat klonowania repozytoriów Git w systemie plików obszaru roboczego](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Udostępnianie notesów i innych plików

Skopiuj i wklej adres URL, aby udostępnić Notes lub plik.  Tylko inni użytkownicy obszaru roboczego będą mogli uzyskać dostęp do tego adresu URL.  Dowiedz się więcej o [udzielaniu dostępu do obszaru roboczego](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Edytowanie notesu

Aby edytować Notes, Otwórz dowolny Notes znajdujący się w sekcji **pliki użytkownika** w obszarze roboczym. Kliknij komórkę, którą chcesz edytować. 

Gdy uruchomione wystąpienie obliczeniowe jest uruchomione, można również użyć uzupełniania kodu obsługiwanego przez [technologię IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)w dowolnym notesie języka Python.

Na pasku narzędzi notesu można również uruchomić Jupyter lub JupyterLab.  Azure Machine Learning nie dostarcza aktualizacji i naprawia usterki z Jupyter lub JupyterLab, ponieważ są to produkty typu open source spoza granicy pomoc techniczna firmy Microsoft.

### <a name="useful-keyboard-shortcuts"></a>Przydatne skróty klawiaturowe

|Klawiatura  |Akcja  |
|---------|---------|
|Shift+Enter     |  Uruchom komórkę       |
|Ctrl + M (Windows)     |  Włącz/Wyłącz zalewkowanie kart w notesie.       |
|Ctrl + Shift + M (Mac & Linux)     |    Włącz/Wyłącz zalewkowanie kart w notesie.     |
|Karta (gdy jest włączona funkcja pułapki kart) | Dodawanie znaku "\t" (wcięcie)
|Karta (gdy zostanie wyłączona karta Zalewka) | Zmień fokus na następny skoncentrowany element (przycisk usuwania komórki, przycisk Uruchom itd.)

## <a name="delete-a-notebook"></a>Usuwanie notesu

*Nie* można usunąć notesów **przykładów** .  Te notesy są częścią programu Studio i są aktualizowane przy każdym publikowaniu nowego zestawu SDK.  

Notesy **plików użytkownika** *można* usuwać w dowolny z następujących sposobów:

* W programie Studio wybierz pozycję **...** na końcu folderu lub pliku.  Upewnij się, że używasz obsługiwanej przeglądarki (Microsoft Edge, Chrome lub Firefox).
* Na dowolnym pasku narzędzi notesu wybierz pozycję [**Otwórz Terminal**](#terminal) , aby uzyskać dostęp do okna terminalu wystąpienia obliczeniowego.
* W Jupyter lub JupyterLab z narzędziami.

## <a name="run-an-experiment"></a>Uruchamianie eksperymentu

Aby uruchomić eksperyment z notesu, należy najpierw połączyć się z uruchomionym [wystąpieniem obliczeniowym](concept-compute-instance.md). Jeśli nie masz wystąpienia obliczeniowego, wykonaj następujące kroki, aby go utworzyć: 

1. Wybierz **+** na pasku narzędzi Notes. 
2. Nazwij obliczenia i wybierz **rozmiar maszyny wirtualnej**. 
3. Wybierz przycisk **Utwórz**.
4. Wystąpienie obliczeniowe jest połączone z notesem automatycznie i można teraz uruchamiać komórki.

Można wyświetlać i używać tylko utworzonych wystąpień obliczeniowych.  **Pliki użytkownika** są przechowywane niezależnie od maszyny wirtualnej i są współdzielone między wszystkimi wystąpieniami obliczeniowymi w obszarze roboczym.

### <a name="view-logs-and-output"></a>Wyświetlanie dzienników i danych wyjściowych

Użyj [widżetów Notes](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , aby wyświetlić postęp przebiegów i dzienników. Widżet jest asynchroniczny i udostępnia aktualizacje do momentu zakończenia szkolenia. Azure Machine Learning widżety są również obsługiwane w Jupyter i JupterLab.

## <a name="change-the-notebook-environment"></a>Zmień środowisko notesu

Pasek narzędzi notesu umożliwia zmianę środowiska, w którym działa Notes.  

Te akcje nie spowodują zmiany stanu notesu ani wartości żadnych zmiennych w notesie:

|Akcja  |Wynik  |
|---------|---------| --------|
|Zatrzymaj jądro     |  Kończy wszystkie uruchomione komórki. Uruchomienie komórki spowoduje automatyczne ponowne uruchomienie jądra. |
|Przejdź do innej sekcji obszaru roboczego     |     Uruchomione komórki są zatrzymane. |

Te akcje spowodują zresetowanie stanu notesu i zresetowanie wszystkich zmiennych w notesie.

|Akcja  |Wynik  |
|---------|---------| --------|
| Zmień jądro | Notes używa nowego jądra |
| Przełączanie obliczeń    |     Notes automatycznie używa nowego obliczenia. |
| Resetowanie obliczeń | Uruchamiany ponownie przy próbie uruchomienia komórki |
| Zatrzymaj Obliczanie     |    Nie uruchomiono żadnych komórek  |
| Otwórz Notes w programie Jupyter lub JupyterLab     |    Notes otwarty na nowej karcie.  |

### <a name="add-new-kernels"></a>Dodaj nowe jądra

W notesie zostaną automatycznie znalezione wszystkie jądra Jupyter zainstalowane w połączonym wystąpieniu obliczeniowym.  Aby dodać jądro do wystąpienia obliczeniowego:

1. Wybierz pozycję [**Otwórz Terminal**](#terminal) na pasku narzędzi notesu.
1. Użyj okna terminalu, aby utworzyć nowe środowisko.
1. Aktywuj środowisko.  Na przykład po utworzeniu `newenv` :

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Można zainstalować dowolne z [dostępnych jądra Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

### <a name="status-indicators"></a>Wskaźniki stanu

Wskaźnik obok listy rozwijanej **obliczenia** pokazuje swój stan.  Stan jest również wyświetlany na liście rozwijanej.  

|Kolor |Stan obliczeń |
|---------|---------| 
| Zielony | Uruchomione obliczenia |
| Red |Obliczanie nie powiodło się | 
| Czarny | Obliczenia zatrzymane |
|  Jasnoniebieski |Tworzenie, uruchamianie, ponowne uruchamianie, Konfigurowanie |
|  Szary |Usuwanie i zatrzymywanie obliczeń |

Wskaźnik obok menu rozwijanego **jądra** pokazuje jego stan.

|Kolor |Stan jądra |
|---------|---------|
|  Zielony |Jądro połączone, bezczynne, zajęte|
|  Szary |Jądro niepołączone |

## <a name="find-compute-details"></a>Znajdź szczegóły obliczeń 

Znajdź szczegółowe informacje o wystąpieniach obliczeniowych na stronie **obliczenia** w programie [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie pierwszego eksperymentu](tutorial-1st-experiment-sdk-train.md)
* [Tworzenie kopii zapasowej magazynu plików za pomocą migawek](../storage/files/storage-snapshots-files.md)
