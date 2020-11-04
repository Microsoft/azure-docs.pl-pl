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
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: d253699199617489947dd95a44d9bcd1eff17334
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325405"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Jak uruchamiać notesy Jupyter w obszarze roboczym


Dowiedz się, jak uruchamiać notesy Jupyter bezpośrednio w obszarze roboczym w programie Azure Machine Learning Studio. Chociaż możesz uruchomić [Jupyter](https://jupyter.org/) lub [JupyterLab](https://jupyterlab.readthedocs.io), możesz również edytować i uruchamiać notesy bez opuszczania obszaru roboczego.

Zobacz, jak to zrobić:

* Tworzenie notesów Jupyter w obszarze roboczym
* Uruchamianie eksperymentu z notesu
* Zmień środowisko notesu
* Znajdowanie szczegółowych informacji o wystąpieniach obliczeniowych używanych do uruchamiania notesów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Tworzenie notesów

W obszarze roboczym Azure Machine Learning Utwórz nowy Notes Jupyter i Rozpocznij pracę. Nowo utworzony Notes jest przechowywany w domyślnym magazynie obszarów roboczych. Ten Notes może być udostępniany wszystkim użytkownikom mającym dostęp do obszaru roboczego. 

Aby utworzyć nowy Notes: 

1. Otwórz obszar roboczy w programie [Azure Machine Learning Studio](https://ml.azure.com).
1. Po lewej stronie wybierz pozycję **notesy**. 
1. Wybierz ikonę  **Utwórz nowy plik** nad listą **plików użytkownika** w sekcji **Moje pliki** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Utwórz nowy plik":::

1. Nazwij plik. 
1. Dla plików Jupyter Notebook wybierz opcję **Notes** jako typ pliku.
1. Wybierz katalog plików.
1. Wybierz przycisk **Utwórz**.

Można również tworzyć pliki tekstowe.  Zaznacz **tekst** jako typ pliku i Dodaj rozszerzenie do nazwy (na przykład myfile.py lub myfile.txt)  

Możesz również przekazać foldery i pliki, w tym notesy, za pomocą narzędzi w górnej części strony notesy.  Notesy i większość typów plików tekstowych są wyświetlane w sekcji Podgląd.  Wersja zapoznawcza nie jest dostępna dla większości innych typów plików.

> [!IMPORTANT]
> Zawartość w notesach i skryptach może potencjalnie odczytywać dane z sesji i uzyskiwać dostęp do danych bez Twojej organizacji na platformie Azure.  Ładuj tylko pliki z zaufanych źródeł. Aby uzyskać więcej informacji, zobacz [Secure Best Practices](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Klonowanie próbek

Obszar roboczy zawiera folder **Samples** z notesami, które ułatwiają zapoznanie się z zestawem SDK i służą jako przykłady dla własnych projektów uczenia maszynowego.  Można sklonować te notesy do własnego folderu w kontenerze magazynu obszaru roboczego.  

Aby zapoznać się z przykładem, zobacz [Samouczek: Tworzenie pierwszego eksperymentu z](tutorial-1st-experiment-sdk-setup.md#azure)tablicą.

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Używaj plików z narzędzia Git i wersji plików my

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

1. Jeśli nie widzisz ikony, wybierz pozycję **...** z prawej strony elementu docelowego obliczeń, a następnie wybierz pozycję **Otwórz Terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Otwórz Terminal z...":::


Dowiedz się więcej [na temat klonowania repozytoriów Git w systemie plików obszaru roboczego](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Kopiowanie i wklejanie w terminalu

> * Windows: `Ctrl-Insert` do kopiowania i używania `Ctrl-Shift-v` lub `Shift-Insert` do wklejania.
> * Mac OS: `Cmd-c` kopiowanie i `Cmd-v` wklejanie.
> * Program FireFox/IE może prawidłowo obsługiwać uprawnienia do Schowka.

### <a name="share-notebooks-and-other-files"></a>Udostępnianie notesów i innych plików

Skopiuj i wklej adres URL, aby udostępnić Notes lub plik.  Tylko inni użytkownicy obszaru roboczego mogą uzyskać dostęp do tego adresu URL.  Dowiedz się więcej o [udzielaniu dostępu do obszaru roboczego](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Edytowanie notesu

Aby edytować Notes, Otwórz dowolny Notes znajdujący się w sekcji **pliki użytkownika** w obszarze roboczym. Kliknij komórkę, którą chcesz edytować. 

Notes można edytować bez nawiązywania połączenia z wystąpieniem obliczeniowym.  Gdy chcesz uruchomić komórki w notesie, wybierz lub Utwórz wystąpienie obliczeniowe.  W przypadku wybrania zatrzymanego wystąpienia obliczeniowego zostanie ono automatycznie uruchomione po uruchomieniu pierwszej komórki.

Po uruchomieniu wystąpienia obliczeniowego można także użyć uzupełniania kodu obsługiwanego przez funkcję [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)w dowolnym notesie języka Python.

Na pasku narzędzi notesu można również uruchomić Jupyter lub JupyterLab.  Azure Machine Learning nie dostarcza aktualizacji i naprawia usterki z Jupyter lub JupyterLab, ponieważ są to produkty typu open source spoza granicy pomoc techniczna firmy Microsoft.

### <a name="focus-mode"></a>Tryb koncentracji uwagi

Użyj trybu koncentracji uwagi, aby rozwinąć bieżący widok, dzięki czemu możesz skupić się na aktywnych kartach. Tryb koncentracji uwagi powoduje ukrycie Eksploratora plików notesów.

1. Na pasku narzędzi okna terminalu wybierz **tryb koncentracji uwagi** , aby włączyć tryb koncentracji uwagi. W zależności od szerokości okna może się to znajdować w elemencie menu **...** na pasku narzędzi.
1. W trybie koncentracji uwagi Wróć do widoku standardowego, wybierając pozycję **widok standardowy**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Przełącz tryb koncentracji uwagi/widok standardowy":::


### <a name="use-intellisense"></a>Korzystanie z funkcji IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) to pomoc dla uzupełniania kodu, która obejmuje wiele funkcji: członków listy, informacji o parametrach, szybkich informacji i kompletnego wyrazu. Te funkcje pozwalają dowiedzieć się więcej o kodzie, którego używasz, śledzić parametry, które wpisujesz, i dodawać wywołania do właściwości i metod za pomocą tylko kilku naciśnięć klawiszy.  

Podczas wpisywania kodu naciśnij klawisze CTRL + SPACJA, aby wyzwolić funkcję IntelliSense.

### <a name="clean-your-notebook-preview"></a>Oczyść Notes (wersja zapoznawcza)

> [!IMPORTANT]
> Funkcja zbierania jest obecnie w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W trakcie tworzenia notesu zazwyczaj możesz kończyć się komórkami używanymi do eksploracji lub debugowania danych. Funkcja *zbierania* pomoże Ci w wykorzystaniu czystego notesu bez takich niezbędnych komórek.

1. Uruchom wszystkie komórki notesu.
1. Wybierz komórkę zawierającą kod, który ma zostać uruchomiony w nowym notesie. Na przykład kod, który przesyła eksperyment lub może być kodem, który rejestruje model.
1. Wybierz ikonę **Zbierz** , która pojawia się na pasku narzędzi komórki.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Zrzut ekranu: wybierz ikonę zbierania":::
1. Wprowadź nazwę nowego notesu "zebrana".  

Nowy notes zawiera tylko komórki kodu, a wszystkie komórki wymagane do wygenerowania tych samych wyników co Komórka wybrana do zebrania.

### <a name="save-and-checkpoint-a-notebook"></a>Zapisz i Utwórz punkt kontrolny notesu

Azure Machine Learning tworzy plik punktu kontrolnego podczas tworzenia pliku *ipynb* .

Na pasku narzędzi Notes wybierz menu, a następnie **Zapisz plik &gt; i punkt kontrolny** , aby ręcznie zapisać Notes i dodać plik punktu kontrolnego skojarzony z notesem.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Zrzut ekranu narzędzia zapisywania na pasku narzędzi notesu":::

Każdy Notes jest automatycznie zapisywany co 30 sekund. Funkcja autozapisywania aktualizuje tylko początkowy plik *ipynb* , a nie plik punktu kontrolnego.
 
Wybierz **punkty kontrolne** w menu Notes, aby utworzyć nazwany punkt kontrolny i przywrócić Notes do zapisanego punktu kontrolnego.


### <a name="useful-keyboard-shortcuts"></a>Przydatne skróty klawiaturowe

|Klawiatura  |Akcja  |
|---------|---------|
|Shift+Enter     |  Uruchom komórkę       |
|Ctrl+Space | Aktywuj technologię IntelliSense |
|Ctrl + M (Windows)     |  Włącz/Wyłącz zalewkowanie kart w notesie.       |
|Ctrl + Shift + M (Mac & Linux)     |    Włącz/Wyłącz zalewkowanie kart w notesie.     |
|Karta (gdy jest włączona funkcja pułapki kart) | Dodawanie znaku "\t" (wcięcie)
|Karta (gdy zostanie wyłączona karta Zalewka) | Zmień fokus na następny skoncentrowany element (przycisk usuwania komórki, przycisk Uruchom itd.)

## <a name="delete-a-notebook"></a>Usuwanie notesu

*Nie* można usunąć notesów **przykładów** .  Te notesy są częścią programu Studio i są aktualizowane przy każdym publikowaniu nowego zestawu SDK.  

Notesy **plików użytkownika** *można* usuwać w dowolny z następujących sposobów:

* W programie Studio wybierz pozycję **...** na końcu folderu lub pliku.  Upewnij się, że używasz obsługiwanej przeglądarki (Microsoft Edge, Chrome lub Firefox).
* Na dowolnym pasku narzędzi notesu wybierz pozycję [**Otwórz Terminal**](#terminal)  , aby uzyskać dostęp do okna terminalu wystąpienia obliczeniowego.
* W Jupyter lub JupyterLab z narzędziami.

## <a name="run-an-experiment"></a>Uruchamianie eksperymentu

Aby uruchomić eksperyment z notesu, należy najpierw połączyć się z uruchomionym [wystąpieniem obliczeniowym](concept-compute-instance.md). Jeśli nie masz wystąpienia obliczeniowego, wykonaj następujące kroki, aby go utworzyć: 

1. Wybierz **+** na pasku narzędzi Notes. 
2. Nazwij obliczenia i wybierz **rozmiar maszyny wirtualnej**. 
3. Wybierz przycisk **Utwórz**.
4. Wystąpienie obliczeniowe jest połączone z notesem automatycznie i można teraz uruchamiać komórki.

Można wyświetlać i używać tylko utworzonych wystąpień obliczeniowych.  **Pliki użytkownika** są przechowywane niezależnie od maszyny wirtualnej i są współdzielone między wszystkimi wystąpieniami obliczeniowymi w obszarze roboczym.

### <a name="view-logs-and-output"></a>Wyświetlanie dzienników i danych wyjściowych

Użyj [widżetów Notes](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) , aby wyświetlić postęp przebiegów i dzienników. Widżet jest asynchroniczny i udostępnia aktualizacje do momentu zakończenia szkolenia. Azure Machine Learning widżety są również obsługiwane w Jupyter i JupterLab.

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
1. Użyj okna terminalu, aby utworzyć nowe środowisko.  Na przykład poniższy kod tworzy `newenv` :
    ```shell
    conda create -y --name newenv
    ```
1. Aktywuj środowisko.  Na przykład po utworzeniu `newenv` :

    ```shell
    conda activate newenv
    ```
1. Zainstaluj pakiet PIP i ipykernel w nowym środowisku i Utwórz jądro dla tej Conda ENV

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> Aby zarządzać pakietami w ramach notesu, użyj funkcji **% PIP** lub **% Conda** Magic w celu automatycznego zainstalowania pakietów w **aktualnie uruchomionym jądrze** zamiast **! PIP** lub **! Conda** , które odwołują się do wszystkich pakietów (w tym pakietów poza aktualnie uruchomionym jądrem).

Można zainstalować dowolne z [dostępnych jądra Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

### <a name="status-indicators"></a>Wskaźniki stanu

Wskaźnik obok listy rozwijanej **obliczenia** pokazuje swój stan.  Stan jest również wyświetlany na liście rozwijanej.  

|Kolor |Stan obliczeń |
|---------|---------| 
| Green (Zielony) | Uruchomione obliczenia |
| Red (Czerwony) |Obliczanie nie powiodło się | 
| Czarnoskórzy | Obliczenia zatrzymane |
|  Jasnoniebieski |Tworzenie, uruchamianie, ponowne uruchamianie, Konfigurowanie |
|  Szary |Usuwanie i zatrzymywanie obliczeń |

Wskaźnik obok menu rozwijanego **jądra** pokazuje jego stan.

|Kolor |Stan jądra |
|---------|---------|
|  Green (Zielony) |Jądro połączone, bezczynne, zajęte|
|  Szary |Jądro niepołączone |

## <a name="find-compute-details"></a>Znajdź szczegóły obliczeń 

Znajdź szczegółowe informacje o wystąpieniach obliczeniowych na stronie **obliczenia** w programie [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie pierwszego eksperymentu](tutorial-1st-experiment-sdk-train.md)
* [Tworzenie kopii zapasowej magazynu plików za pomocą migawek](../storage/files/storage-snapshots-files.md)