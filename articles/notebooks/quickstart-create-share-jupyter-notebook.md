---
title: Tworzenie i udostępnianie Jupyter Notebook w wersji zapoznawczej Azure Notebooks
description: Szybko Twórz i uruchamiaj Jupyter Notebook w wersji zapoznawczej Azure Notebooks, a następnie Udostępnij ten Notes innym osobom.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88589337"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Szybki Start: Tworzenie i udostępnianie notesu w podglądzie Azure Notebooks

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

W tym przewodniku szybki start utworzysz i uruchomisz Jupyter Notebook na Azure Notebooks, a następnie udostępnisz ten Notes innym osobom. Jupyter umożliwia łatwe łączenie tekstu z promocji, kodu wykonywalnego, trwałych danych, grafiki i wizualizacji na jednej kanwie możliwej do przedziału. Azure Notebooks to bezpłatna usługa hostowana umożliwiająca opracowywanie i uruchamianie notesów Jupyter w chmurze bez konieczności instalacji.

## <a name="prerequisites"></a>Wymagania wstępne
Brak.

## <a name="create-a-new-project-and-notebook"></a>Tworzenie nowego projektu i notesu

1. Przejdź do [witryny Azure Notebooks ( https://notebooks.azure.com) ](https://notebooks.azure.com) i zaloguj się. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). Przycisk może się pojawić tylko wtedy, **+** gdy okno przeglądarki jest wąskie:

    ![Polecenie nowego projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz**:

   - **Nazwa projektu**: Hello World w języku Python
   - **Identyfikator projektu**: Hello-World-Python
   - **Projekt publiczny**: (wyczyszczone)
   - **Utwórz element Readme.MD**: (wyczyszczony)

     ![Okno podręczne nowego projektu z wypełnionymi szczegółami](media/quickstarts/new-project-popup.png)

1. Po kilku chwilach Azure Notebooks przechodzi do nowego projektu. Dodaj Notes do projektu, wybierając listę rozwijaną **+ Nowy** (która może być wyświetlana tylko jako **+** ), a następnie wybierając opcję **Notes**:

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Nowy, pusty projekt i Dodaj Notes polecenie." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. W wyświetlonym oknie podręcznym **Utwórz nowy Notes** wprowadź nazwę pliku dla notesu, na przykład *HelloWorldInPython. ipynb* (*. ipynb* oznacza Notes IronPython (Jupyter)), a następnie wybierz opcję **Python 3,6** dla języka (nazywanego również *jądrem*):

    ![Okno podręczne Tworzenie nowego notesu](media/quickstarts/new-notebook-popup.png)

1. Wybierz pozycję **Nowy** , aby zakończyć tworzenie notesu, który zostanie wyświetlony na liście plików projektu:

    ![Nowy Notes pojawiający się na liście plików projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

1. Wybierz nowy Notes, który ma być uruchamiany w edytorze; wybrany jądro (Python 3,6 w tym przykładzie) jest automatycznie aktywowany dla tego notesu:

    ![Widok nowego notesu w Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Domyślnie Notes ma jedną pustą komórkę kodu. **Aby zmienić**typ komórki na rozbiciu, Użyj listy rozwijanej Typ komórki, **Aby wybrać opcję**przestawka:

    ![Zmiana typu komórki w nowym notesie](media/quickstarts/create-notebook-cell-type.png)

1. Wprowadź lub wklej następujący tekst nagłówka do komórki:

    ```markdown
    # Hello World in Python
    ```

1. Ponieważ edytujesz opcję promocji, tekst jest wyświetlany jako nagłówek z "#". Aby renderować tę wartość w formacie HTML, wybierz przycisk **Uruchom** . Azure Notebooks następnie automatycznie tworzy nową komórkę kodu:

    ![Przycisk Run dla komórki i renderowanej promocji](media/quickstarts/run-cell-markdown-render.png)

1. W komórce kod wprowadź następujący kod w języku Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Wybierz polecenie **Uruchom** (skrót klawiaturowy: SHIFT + ENTER), aby uruchomić kod. Poniżej komórki powinny być widoczne pomyślne dane wyjściowe podobne do następującego tekstu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Wybierz ikonę Zapisz, aby zapisać swoją służbę:

    ![Ikona zapisywania na pasku narzędzi Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Wybierz **File**  >  polecenie menu**Zamknij i** Zatrzymaj, aby zatrzymać serwer i zamknąć okno przeglądarki.

## <a name="share-the-notebook"></a>Udostępnianie notesu

Aby udostępnić Notes, w razie potrzeby przełącz się z powrotem do strony projektu, kliknij prawym przyciskiem myszy plik notesu, wybierz polecenie **Kopiuj link** (skrót klawiaturowy: t), a następnie wklej ten link do odpowiedniego komunikatu (wiadomości e-mail, wiadomości błyskawicznych itp.).

Na stronie projekt można również użyć menu **Udostępnij** , aby uzyskać link, utworzyć wiadomość e-mail z linkiem lub uzyskać kod osadzania HTML i promocji:

![Project Share — polecenie](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie Jupyter Notebook w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
