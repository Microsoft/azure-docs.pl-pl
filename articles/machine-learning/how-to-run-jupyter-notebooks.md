---
title: Uruchamianie notesów Jupyter w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić Notes Jupyter bez opuszczania obszaru roboczego w programie Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 18ccadcf43d41c677a665ed068d093f51389b576
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657336"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Uruchamianie notesów Jupyter w obszarze roboczym

Dowiedz się, jak uruchamiać notesy Jupyter bezpośrednio w obszarze roboczym w programie Azure Machine Learning Studio. Chociaż możesz uruchomić [Jupyter](https://jupyter.org/) lub [JupyterLab](https://jupyterlab.readthedocs.io), możesz również edytować i uruchamiać notesy bez opuszczania obszaru roboczego.

Aby uzyskać informacje na temat tworzenia plików i zarządzania nimi, w tym notesów, zobacz [Tworzenie plików i zarządzanie nimi w obszarze roboczym](how-to-manage-files.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Edytowanie notesu

Aby edytować Notes, Otwórz dowolny Notes znajdujący się w sekcji **pliki użytkownika** w obszarze roboczym. Kliknij komórkę, którą chcesz edytować.  Jeśli nie masz żadnych notesów w tej sekcji, zobacz [Tworzenie plików i zarządzanie nimi w obszarze roboczym](how-to-manage-files.md).

Notes można edytować bez nawiązywania połączenia z wystąpieniem obliczeniowym.  Gdy chcesz uruchomić komórki w notesie, wybierz lub Utwórz wystąpienie obliczeniowe.  W przypadku wybrania zatrzymanego wystąpienia obliczeniowego zostanie ono automatycznie uruchomione po uruchomieniu pierwszej komórki.

Po uruchomieniu wystąpienia obliczeniowego można także użyć uzupełniania kodu obsługiwanego przez funkcję [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)w dowolnym notesie języka Python.

Na pasku narzędzi notesu można również uruchomić Jupyter lub JupyterLab.  Azure Machine Learning nie dostarcza aktualizacji i naprawia usterki z Jupyter lub JupyterLab, ponieważ są to produkty typu open source spoza granicy pomoc techniczna firmy Microsoft.

## <a name="focus-mode"></a>Tryb koncentracji uwagi

Użyj trybu koncentracji uwagi, aby rozwinąć bieżący widok, dzięki czemu możesz skupić się na aktywnych kartach. Tryb koncentracji uwagi powoduje ukrycie Eksploratora plików notesów.

1. Na pasku narzędzi okna terminalu wybierz **tryb koncentracji uwagi** , aby włączyć tryb koncentracji uwagi. W zależności od szerokości okna może się to znajdować w elemencie menu **...** na pasku narzędzi.
1. W trybie koncentracji uwagi Wróć do widoku standardowego, wybierając pozycję **widok standardowy**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Przełącz tryb koncentracji uwagi/widok standardowy":::

## <a name="use-intellisense"></a>Korzystanie z funkcji IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) to pomoc dla uzupełniania kodu, która obejmuje wiele funkcji: członków listy, informacji o parametrach, szybkich informacji i kompletnego wyrazu. Te funkcje pozwalają dowiedzieć się więcej o kodzie, którego używasz, śledzić parametry, które wpisujesz, i dodawać wywołania do właściwości i metod za pomocą tylko kilku naciśnięć klawiszy.  

Podczas wpisywania kodu naciśnij klawisze CTRL + SPACJA, aby wyzwolić funkcję IntelliSense.

## <a name="clean-your-notebook-preview"></a>Oczyść Notes (wersja zapoznawcza)

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

## <a name="save-and-checkpoint-a-notebook"></a>Zapisz i Utwórz punkt kontrolny notesu

Azure Machine Learning tworzy plik punktu kontrolnego podczas tworzenia pliku *ipynb* .

Na pasku narzędzi Notes wybierz menu, a następnie **Zapisz plik &gt; i punkt kontrolny** , aby ręcznie zapisać Notes i dodać plik punktu kontrolnego skojarzony z notesem.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Zrzut ekranu narzędzia zapisywania na pasku narzędzi notesu":::

Każdy Notes jest automatycznie zapisywany co 30 sekund. Funkcja autozapisywania aktualizuje tylko początkowy plik *ipynb* , a nie plik punktu kontrolnego.
 
Wybierz **punkty kontrolne** w menu Notes, aby utworzyć nazwany punkt kontrolny i przywrócić Notes do zapisanego punktu kontrolnego.

## <a name="export-a-notebook"></a>Eksportowanie notesu

Na pasku narzędzi Notes wybierz menu, a następnie pozycję **Eksportuj jako** , aby wyeksportować Notes jako dowolny z obsługiwanych typów:

* Notes
* Python
* HTML
* Pian

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Eksportowanie notesu na komputer":::

Wyeksportowany plik zostanie zapisany na komputerze.

## <a name="run-a-notebook-or-python-script"></a>Uruchamianie notesu lub skryptu języka Python

Aby uruchomić Notes lub skrypt języka Python, należy najpierw nawiązać połączenie z uruchomionym [wystąpieniem obliczeniowym](concept-compute-instance.md).

* Jeśli nie masz wystąpienia obliczeniowego, wykonaj następujące kroki, aby go utworzyć:

    1. Na pasku narzędzi notesu lub skryptu z prawej strony listy rozwijanej obliczenia wybierz pozycję **+ nowe obliczenia**. W zależności od rozmiaru ekranu może się to znajdować w menu. **..** .
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Utwórz nowe obliczenie":::
    1. Nazwij obliczenia i wybierz **rozmiar maszyny wirtualnej**. 
    1. Wybierz przycisk **Utwórz**.
    1. Wystąpienie obliczeniowe jest połączone z plikiem automatycznie.  Teraz można uruchamiać komórki notesu lub skrypt języka Python przy użyciu narzędzia z lewej strony wystąpienia obliczeniowego.

* Jeśli masz zatrzymane wystąpienie obliczeniowe, wybierz pozycję  **Rozpocznij Obliczanie** po prawej stronie listy rozwijanej obliczenia. W zależności od rozmiaru ekranu może się to znajdować w menu. **..** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Uruchom wystąpienie obliczeniowe":::

Można wyświetlać i używać tylko utworzonych wystąpień obliczeniowych.  **Pliki użytkownika** są przechowywane niezależnie od maszyny wirtualnej i są współdzielone między wszystkimi wystąpieniami obliczeniowymi w obszarze roboczym.

### <a name="view-logs-and-output"></a>Wyświetlanie dzienników i danych wyjściowych

Użyj [widżetów Notes](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) , aby wyświetlić postęp przebiegów i dzienników. Widżet jest asynchroniczny i udostępnia aktualizacje do momentu zakończenia szkolenia. Azure Machine Learning widżety są również obsługiwane w Jupyter i JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Zrzut ekranu: widżet Jupyter Notes ":::

## <a name="explore-variables-in-the-notebook"></a>Eksplorowanie zmiennych w notesie

Na pasku narzędzi Notes Użyj narzędzia **Eksplorator zmiennych** , aby wyświetlić nazwę, typ, długość i przykładowe wartości dla wszystkich zmiennych, które zostały utworzone w Twoim notesie.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Zrzut ekranu: Narzędzie Eksplorator zmiennych":::

Wybierz narzędzie, aby wyświetlić okno Eksplorator zmiennych.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Zrzut ekranu: okno Eksplorator zmiennych":::

## <a name="navigate-with-a-toc"></a>Nawigowanie po spisie treści

Na pasku narzędzi Notes Użyj narzędzia spis  **treści** do wyświetlania lub ukrywania spisu treści.  Aby dodać tabelę do spisu treści, należy uruchomić komórkę z przenoszącą się do promocji z nagłówkiem. Kliknij wpis w tabeli, aby przewinąć do tej komórki w notesie.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Zrzut ekranu: Spis treści w notesie":::

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

## <a name="add-new-kernels"></a>Dodaj nowe jądra

[Użyj terminalu ](how-to-access-terminal.md#add-new-kernels) , aby utworzyć i dodać nowe jądra do wystąpienia obliczeniowego. W notesie zostaną automatycznie znalezione wszystkie jądra Jupyter zainstalowane w połączonym wystąpieniu obliczeniowym.

Użyj listy rozwijanej jądra po prawej stronie, aby zmienić dowolne z zainstalowanych jądra.  


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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Jeśli nie możesz połączyć się z notesem, upewnij się, że komunikacja gniazda internetowego **nie** jest wyłączona. Aby można było korzystać z funkcji Jupyter wystąpienia obliczeniowego, musi być włączona komunikacja między gniazdami sieci Web. Upewnij się, że sieć zezwala na połączenia protokołu WebSocket z *. instances.azureml.net i *. instances.azureml.ms. 

* Gdy wystąpienie obliczeniowe zostanie wdrożone w prywatnym obszarze roboczym łącza, można uzyskać do niego dostęp tylko z poziomu sieci wirtualnej. Jeśli używasz niestandardowego pliku DNS lub hosta, Dodaj wpis dla <nazwa wystąpienia>. <region> . instances.azureml.ms z prywatnym adresem IP prywatnego punktu końcowego obszaru roboczego. Aby uzyskać więcej informacji, zobacz artykuł [niestandardowy DNS](./how-to-custom-dns.md?tabs=azure-cli) .
    
## <a name="next-steps"></a>Następne kroki

* [Uruchamianie pierwszego eksperymentu](tutorial-1st-experiment-sdk-train.md)
* [Tworzenie kopii zapasowej magazynu plików za pomocą migawek](../storage/files/storage-snapshots-files.md)
* [Praca w bezpiecznych środowiskach](./how-to-secure-training-vnet.md#compute-instance)