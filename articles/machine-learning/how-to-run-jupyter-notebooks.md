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
ms.openlocfilehash: 257fc6544061c2ef9c3fdbfb8c33bc06ed2db6e3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566339"
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

Użyj [widżetów Notes](/python/api/azureml-widgets/azureml.widgets) , aby wyświetlić postęp przebiegów i dzienników. Widżet jest asynchroniczny i udostępnia aktualizacje do momentu zakończenia szkolenia. Azure Machine Learning widżety są również obsługiwane w Jupyter i JupterLab.

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

## <a name="useful-keyboard-shortcuts"></a>Przydatne skróty klawiaturowe
Podobnie jak w przypadku notesów Jupyter, notesy Azure Machine Learning Studio zawierają modalny interfejs użytkownika. Klawiatura wykonuje różne czynności w zależności od trybu, w którym znajduje się komórka notesu. Azure Machine Learning Studio notesy obsługują następujące dwa tryby dla danej komórki kodu: Tryb poleceń i tryb edycji.

### <a name="command-mode-shortcuts"></a>Skróty trybu polecenia

Komórka jest w trybie polecenia, gdy nie ma kursora tekstu z monitem o wpisanie. Gdy komórka jest w trybie poleceń, można edytować Notes jako całość, ale nie do pojedynczych komórek. Przejdź do trybu polecenia, naciskając `ESC` lub używając myszy, aby zaznaczyć poza obszarem edytora komórki.  Lewa krawędź aktywnej komórki to niebieska i pełna, a jej przycisk **Run** jest niebieski.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Komórka Notes w trybie poleceń ":::

| Skrót                      | Opis                          |
| ----------------------------- | ------------------------------------|
| Enter                         | Przechodzenie do trybu edycji             |        
| Shift + Enter                 | Uruchom komórkę, wybierz poniżej         |     
| Control/Command + Enter       | Uruchom komórkę                            |
| Alt + Enter                   | Uruchom komórkę, Wstaw komórkę kodu poniżej    |
| Control/Command + Alt + Enter | Uruchom komórkę, Wstaw komórkę z promocji poniżej|
| Alt + R                       | Uruchom wszystko      |                       
| Y                             | Konwertuj komórkę na kod    |                         
| M                             | Konwertuj komórkę na płatną  |                       
| W górę/K                          | Zaznacz komórkę powyżej    |               
| W dół/J                        | Wybierz komórkę poniżej    |               
| A                             | Wstaw komórkę kodu powyżej  |            
| B                             | Wstaw komórkę kodu poniżej   |           
| Control/Command + Shift + A   | Wstaw powyższą komórkę z promocji    |      
| Control/Command + Shift + B   | Wstaw komórkę z promocji poniżej   |       
| X                             | Wytnij wybraną komórkę    |               
| C                             | Kopiuj wybraną komórkę   |               
| Shift + V                     | Wklej wybraną komórkę powyżej           |
| V                             | Wklej wybraną komórkę poniżej    |       
| D D                           | Usuń wybraną komórkę|                
| O                             | Przełącz dane wyjściowe         |              
| Shift + O                     | Przełącz przewijanie danych wyjściowych   |          
| i                           | Jądro przerwania |                   
| 0 0                           | Uruchom ponownie jądro |                     
| SHIFT + SPACJA                 | Przewiń w górę  |                         
| Miejsce                         | Przewiń w dół|
| Tab                           | Zmień fokus na następny skoncentrowany element (gdy karta jest wyłączona)|
| Control/Command + S           | Zapisz Notes |                      
| 1                             | Zmień na H1|                       
| 2                             | Zmień na H2|                        
| 3                             | Zmień na H3|                        
| 4                             | Zmień na H4 |                       
| 5                             | Zmień na h |                       
| 6                             | Zmień na H6 — |                       

### <a name="edit-mode-shortcuts"></a>Edytuj skróty trybu

Tryb edycji jest wskazywany przez kursor tekstowy z monitem o wpisanie w obszarze edytora. Gdy komórka jest w trybie edycji, możesz wpisać ją w komórce. Przejdź do trybu edycji, naciskając `Enter` lub używając myszy, aby wybrać obszar edytora komórki. Lewa krawędź aktywnej komórki jest zielona i zakreskowana, a jej przycisk **uruchamiania** jest zielony. Zobaczysz również monit kursora w komórce w trybie edycji.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Komórka notesu w trybie edycji":::

Korzystając z następujących skrótów klawiaturowych, można łatwiej nawigować i uruchamiać kod w notesach Azure Machine Learning w trybie edycji.

| Skrót                      | Opis|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | Przejdź do trybu polecenia|  
| Kontrolka/polecenie + spacja       | Aktywuj technologię IntelliSense |
| Shift + Enter                 | Uruchom komórkę, wybierz poniżej |                         
| Control/Command + Enter       | Uruchom komórkę  |                                      
| Alt + Enter                   | Uruchom komórkę, Wstaw komórkę kodu poniżej  |              
| Control/Command + Alt + Enter | Uruchom komórkę, Wstaw komórkę z promocji poniżej  |          
| Alt + R                       | Uruchom wszystkie komórki     |                              
| W górę                            | Przenieś kursor w górę lub w poprzedniej komórce    |             
| W dół                          | Przenieś kursor w dół lub w następnej komórce |                  
| Control/Command + S           | Zapisz Notes   |                                
| Kontrolka/polecenie + Strzałka w górę          | Przejdź do początku komórki   |                             
| Control/Command + Strzałka w dół        | Przejdź do końca komórki |                                 
| Tab                           | Uzupełnianie kodu lub wcięcie (jeśli Zalewka karty jest włączona) |
| Control/Command + M           | Włącz/Wyłącz pułapkę kart  |                       
| Control/Command +]           | Wyświetlane |                                         
| Control/Command + [           | Zmniejsz wcięcie  |                                        
| Control/Command + A           | Zaznacz wszystko|                                      
| Control/Command + Z           | Cofnij |                                           
| Control/Command + Shift + Z   | Ponów |                                           
| Control/Command + Y           | Ponów |                                           
| Kontrolka/polecenie + Strona główna        | Przejdź do początku komórki|                                
| Kontrolka/polecenie + koniec         | Przejdź do końca komórki   |                               
| Kontrolka/polecenie + w lewo        | Przejdź o jedno słowo w lewo |                               
| Kontrolka/polecenie + w prawo       | Przejdź o jedno słowo w prawo |                              
| Control/Command + Backspace   | Usuń słowo przed |                             
| Control/Command + Delete      | Usuń słowo po |                              
| Control/Command +/           | Przełącz komentarz w komórce

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Jeśli nie możesz połączyć się z notesem, upewnij się, że komunikacja gniazda internetowego **nie** jest wyłączona. Aby można było korzystać z funkcji Jupyter wystąpienia obliczeniowego, musi być włączona komunikacja między gniazdami sieci Web. Upewnij się, że sieć zezwala na połączenia protokołu WebSocket z *. instances.azureml.net i *. instances.azureml.ms. 

* Gdy wystąpienie obliczeniowe zostanie wdrożone w prywatnym obszarze roboczym łącza, można [uzyskać do niego dostęp tylko z poziomu sieci wirtualnej](./how-to-secure-training-vnet.md#compute-instance). Jeśli używasz niestandardowego pliku DNS lub hosta, Dodaj wpis dla < nazwa wystąpienia >. < regionu >. instances.azureml.ms z prywatnym adresem IP obszaru prywatnego punktu roboczego. Aby uzyskać więcej informacji, zobacz artykuł [niestandardowy DNS](./how-to-custom-dns.md?tabs=azure-cli) .
    
## <a name="next-steps"></a>Następne kroki

* [Uruchamianie pierwszego eksperymentu](tutorial-1st-experiment-sdk-train.md)
* [Tworzenie kopii zapasowej magazynu plików za pomocą migawek](../storage/files/storage-snapshots-files.md)
* [Praca w bezpiecznych środowiskach](./how-to-secure-training-vnet.md#compute-instance)