---
title: Samouczek — Tworzenie i uruchamianie Jupyter Notebook-Azure Notebooks Preview
description: Dowiedz się, jak utworzyć i uruchomić Jupyter Notebook w wersji zapoznawczej Azure Notebooks, która pokazuje proces regresji liniowej w nauce danych.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: cb8be1618de7e95a7096e4ea9dec78bf4aa277f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88589268"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Samouczek: Tworzenie i uruchamianie Jupyter Notebook przy użyciu języka Python

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ten samouczek przeprowadzi Cię przez proces używania Azure Notebooks, aby utworzyć kompletny Jupyter Notebook, który demonstruje prostą regresję liniową. W ramach tego samouczka zapoznajesz się z interfejsem użytkownika Jupyter Notebook, który obejmuje tworzenie różnych komórek, uruchamianie komórek i prezentowanie notesu jako pokazu slajdów.

Ukończony Notes można znaleźć w [przykładach Azure Notebooks GitHub](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Ten samouczek, jednak rozpoczyna się od nowego projektu i pustego notesu, aby można było go utworzyć krok po kroku.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie notesu projektu z niektórymi przykładowymi danymi
> * Korzystanie z interfejsu notesu do tworzenia różnych typów komórek
> * Uruchamianie notesu
> * Zapisz Notes
> * Debuguj Notes w Visual Studio Code

## <a name="create-the-project"></a>Tworzenie projektu

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). przycisk może się pojawić tylko wtedy, **+** gdy okno przeglądarki jest wąskie:

    ![Polecenie nowego projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz**:

   - **Nazwa projektu**: przykład regresji liniowej — Cricket chirps
   - **Identyfikator projektu**: regresja liniowa — przykład
   - **Projekt publiczny**: (wyczyszczone)
   - **Utwórz element Readme.MD**: (wyczyszczony)

1. Po kilku chwilach Azure Notebooks przechodzi do nowego projektu.

## <a name="create-the-data-file"></a>Utwórz plik danych

Model regresji liniowej tworzony w notesie pobiera dane z pliku w projekcie o nazwie *cricket_chirps.csv*. Ten plik można utworzyć, kopiując go z przykładów usługi [GitHub-Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)lub wprowadzając dane bezpośrednio. W poniższych sekcjach opisano oba podejścia.

### <a name="upload-the-data-file"></a>Przekaż plik danych

1. Na pulpicie nawigacyjnym projektu w Azure Notebooks wybierz pozycję **Przekaż**  >  **z adresu URL**
1. W oknie podręcznym wprowadź następujący adres URL w polu **adres URL pliku** i *cricket_chirps.csv* w polu **Nazwa pliku**, a następnie wybierz pozycję **gotowe**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Plik *cricket_chirps.csv* powinien teraz pojawić się na liście plików projektu:

    ![Nowo utworzony plik CSV wyświetlany na liście plików projektu](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Tworzenie pliku od podstaw

1. Na pulpicie nawigacyjnym projektu w Azure Notebooks wybierz pozycję **+ Nowy**  >  **pusty plik**
1. Pole pojawi się na liście plików projektu. Wprowadź *cricket_chirps.csv* i naciśnij klawisz ENTER.
1. Kliknij prawym przyciskiem myszy *cricket_chirps.csv* i wybierz polecenie **Edytuj plik**.
1. W wyświetlonym edytorze wprowadź następujące dane:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Wybierz pozycję **Zapisz plik** , aby zapisać plik, i wróć do pulpitu nawigacyjnego projektu.

## <a name="install-project-level-packages"></a>Zainstaluj pakiety na poziomie projektu

W notesie można zawsze używać poleceń takich jak `!pip install` w komórce kodu, aby zainstalować wymagane pakiety. Jednak takie polecenia są uruchamiane przy każdym uruchomieniu komórek kodu notesu i mogą zająć dużo czasu. Z tego powodu zamiast tego można instalować pakiety na poziomie projektu przy użyciu `requirements.txt` pliku.

1. Użyj procesu opisanego w temacie [Tworzenie pliku od podstaw](#create-a-file-from-scratch) , aby utworzyć plik o nazwie `requirements.txt` z następującą zawartością:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Możesz również przekazać `requirements.txt` plik z komputera lokalnego, jeśli wolisz, zgodnie z opisem na stronie [przekazywanie pliku danych](#upload-the-data-file).

1. Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**.
1. W wyświetlonym oknie podręcznym wybierz kartę **środowisko** , a następnie wybierz pozycję **+ Dodaj**.
1. W pierwszej kontrolce listy rozwijanej (operacja) w obszarze **czynności konfiguracyjne środowiska**wybierz **Requirements.txt**.
1. W drugiej kontrolce listy rozwijanej (nazwa pliku) wybierz *requirements.txt* (utworzony plik).
1. W trzeciej kontrolce listy rozwijanej (wersja języka Python) Wybierz środowisko **Python w wersji 3,6**.
1. Wybierz pozycję **Zapisz**.

![Karta środowisko ustawień projektu określająca plik requirements.txt](media/tutorial/tutorial-requirements-txt.png)

W przypadku tego kroku instalacji każdy Notes uruchomiony w projekcie zostanie uruchomiony w środowisku, w którym są zainstalowane te pakiety.

## <a name="create-and-run-a-notebook"></a>Tworzenie i uruchamianie notesu

W przypadku gotowości pliku danych i ustawienia środowiska projektowego możesz teraz utworzyć i otworzyć Notes.

1. Na pulpicie nawigacyjnym projektu wybierz pozycję **+ Nowy**  >  **Notes**.
1. W oknie podręcznym wprowadź *przykład regresji liniowej — Cricket chirps. ipynb* dla **nazwy elementu**, wybierz opcję **Python 3,6** dla języka, a następnie wybierz pozycję **Nowy**.
1. Po pojawieniu się nowego notesu na liście plików wybierz go, aby uruchomić Notes. Zostanie automatycznie otwarta nowa karta przeglądarki.
1. Ponieważ w ustawieniach środowiska istnieje plik *requirements.txt* , zostanie wyświetlony komunikat "Oczekiwanie na zakończenie przygotowywania kontenera". Możesz wybrać **przycisk OK** , aby zamknąć komunikat i kontynuować pracę w notesie. nie można jednak uruchomić komórek kodu, dopóki środowisko nie zostanie skonfigurowane w pełni.
1. Notes zostanie otwarty w interfejsie Jupyter z pojedynczą pustą komórką kodu jako domyślną.

    [![Początkowy widok nowego notesu w Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Samouczek dotyczący interfejsu notesu

Z uruchomionym notesem możesz dodać kod i komórki o promocji, uruchomić te komórki i zarządzać działaniem notesu. Po pierwsze zapoznaj się z interfejsem za kilka minut. Aby uzyskać pełną dokumentację, wybierz **Help**  >  polecenie menu**Pomoc dla notesu** pomocy.

W górnej części okna widoczne są następujące elementy:

(A) nazwa notesu, którą można edytować, klikając pozycję.
(B) przyciski, aby przejść do zawierającego go projektu i pulpitu nawigacyjnego projekty, które otwierają nowe karty w przeglądarce.
(C) menu z poleceniami służącymi do pracy z notesem.
(D) pasek narzędzi ze skrótami dla typowych operacji.
(E) Kanwa edycji zawierającej komórki.
(F) wskaźnik określający, czy Notes jest zaufany (domyślnie **nie jest to zaufana**).
(G) jądro używane do uruchamiania notesu wraz ze wskaźnikiem aktywności.

[![Podstawowe obszary interfejsu użytkownika interfejsu Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter zawiera wbudowany Przewodnik dotyczący głównych elementów interfejsu użytkownika. Rozpocznij pracę z przewodnikiem, wybierając polecenie **Pomoc**  >  **interfejsu użytkownika** i klikając okna podręczne.

Grupy poleceń menu są następujące:

| Menu | Opis |
| --- | --- |
| File | Polecenia służące do zarządzania plikiem notesu, w tym polecenia do tworzenia i kopiowania notesów, wyświetlania podglądu wydruku i pobierania notesu w różnych formatach. |
| Edytuj | Typowe polecenia służące do wycinania, kopiowania i wklejania komórek, znajdowania i zamieniania wartości, zarządzania załącznikami komórek i wstawiania obrazów.  |
| Widok | Polecenia służące do sterowania widocznością różnych części interfejsu użytkownika Jupyter. |
| Insert | Polecenia służące do wstawiania nowej komórki powyżej lub poniżej bieżącej komórki. Te polecenia są często używane podczas tworzenia notesu. |
| Komórka | Różne polecenia **uruchamiania** uruchamiają co najmniej jedną komórkę w różnych kombinacjach. Polecenia **typu komórki** zmieniają typ komórki między **kodem** **, rozróżnieniem i** **nieprzetworzonym NBConvert** (zwykłym tekstem). **Bieżące** dane wyjściowe i **wszystkie polecenia wyjściowe** kontrolują sposób wyświetlania danych wyjściowych w kodzie uruchomienia i zawierają polecenie czyszczenia wszystkich danych wyjściowych. |
| Jądro | Polecenia służące do zarządzania sposobem uruchamiania kodu w jądrze, a także **zmiany jądra** w celu zmiany języka lub wersji środowiska Python używanego do uruchamiania notesu. |
| Dane | Polecenia do przekazywania i pobierania plików z projektu lub sesji. Zobacz [pracy z plikami danych projektu](work-with-project-data-files.md) |
| Widżety | Polecenia do zarządzania [widżetami Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), które zapewniają dodatkowe możliwości wizualizacji, mapowania i wykreślania.|
| Pomoc | Polecenia, które zapewniają pomoc i dokumentację dla interfejsu Jupyter. |

Większość poleceń na pasku narzędzi ma równoważne polecenia menu. Jedynym wyjątkiem jest **Pokaz slajdów i edytowanie**, który jest omawiany w [notesie udostępniania i prezentacji](present-jupyter-notebooks-slideshow.md).

Podczas wypełniania notesu w poniższych sekcjach należy użyć kilku poleceń.

## <a name="create-a-markdown-cell"></a>Utwórz komórkę z promocji

1. Kliknij w pierwszej pustej komórce widocznej na kanwie notesu. Domyślnie komórka jest typem **kodu** , co oznacza, że jest ona zaprojektowana tak, aby zawierała kod możliwy do uruchomienia dla wybranego jądra (Python, R lub F #). Bieżący typ jest wyświetlany na liście rozwijanej Typ na pasku narzędzi:

    ![Lista rozwijana paska narzędzi typu komórka](media/tutorial/tutorial-cell-type-drop-down.png)

1. Zmień typ **komórki na** przestawkę na rozbiciu przy użyciu listy rozwijanej paska narzędzi. Alternatywnie, użyj menu **komórka komórki**  >  **Typ**  >  **promocji** :

    ![Menu Typ komórki — polecenie](media/tutorial/tutorial-cell-type-menu.png)

1. Kliknij komórkę, aby rozpocząć edycję, a następnie wprowadź następujące opcje promocji:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Aby wyrównać tę replikę w formacie HTML dla przeglądarki, wybierz polecenie **Uruchom** na pasku narzędzi lub Użyj **komórki**  >  **Run Cells** . Kod promocji dla formatowania i linków jest teraz wyświetlany, gdy zachodzi taka potrzeba w przeglądarce.

1. Po uruchomieniu ostatniej komórki w notesie Jupyter automatycznie tworzy nową komórkę poniżej tego, który został uruchomiony. Aby uzyskać więcej informacji na temat tej komórki, należy powtórzyć kroki opisane w tej sekcji z uwzględnieniem następującej promocji:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Aby ponownie zmodyfikować tę replikę, kliknij dwukrotnie w renderowanej komórce. Aby renderować kod HTML ponownie po wprowadzeniu zmian, uruchom komórkę.

## <a name="create-a-code-cell-with-commands"></a>Tworzenie komórki kodu za pomocą poleceń

Jak opisano w poprzedniej komórce promocji, możesz uwzględnić polecenia bezpośrednio w notesie. Za pomocą poleceń można instalować pakiety, uruchamiać zwinięcie lub Wget w celu pobierania danych lub innych elementów. Jupyter notesy skutecznie działają na maszynie wirtualnej z systemem Linux, więc masz pełne polecenie systemu Linux, z którym będzie pracować.

1. Wprowadź poniższe polecenia w komórce kodu, która pojawiła się po **uruchomieniu** w poprzedniej komórce promocji. Jeśli nie widzisz nowej komórki, utwórz ją za pomocą **Wstaw**  >  **komórkę Wstaw poniżej** lub Użyj **+** przycisku na pasku narzędzi.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Przed uruchomieniem komórki Utwórz nową komórkę z **+** przyciskiem na pasku narzędzi, ustaw ją na pokrycie, a następnie wprowadź następujące wyjaśnienie:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Wybierz **komórkę**  >  **Uruchom wszystkie** polecenie, aby uruchomić wszystkie komórki w notesie. Zwróć uwagę, że komórki promocji są renderowane jako HTML, a polecenie jest uruchamiane w jądrze i obserwuj wskaźnik jądra zgodnie z opisem w samej promocji:

    ![Wskaźnik zajętości dla jądra notesu](media/tutorial/tutorial-kernel-busy.png)

1. Jest to również nieco czasochłonne dla wszystkich `pip install` poleceń, a ponieważ te pakiety zostały już zainstalowane w środowisku projektu (i ponieważ są również domyślnie uwzględnione w Azure Notebooks), zobaczysz wiele komunikatów, które zostały odczytane, "wymaganie już spełnione". Wszystkie te dane wyjściowe mogą wizualnie rozpraszać, dlatego należy wybrać tę komórkę (za pomocą pojedynczego kliknięcia), a następnie użyć **Cell**  >  **Cell Outputs**  >  **przełączenia** danych wyjściowych komórki komórka, aby ukryć dane wyjściowe. Możesz również użyć polecenia **Wyczyść** w tym samym podmenu, aby całkowicie usunąć dane wyjściowe.

    **Przełącznik** powoduje ukrycie tylko najnowszych danych wyjściowych z komórki; po ponownym uruchomieniu komórki dane wyjściowe zostaną wyświetlone ponownie.

1. Ponieważ pakiety są instalowane w środowisku projektu, należy dodać komentarz do `! pip install` poleceń przy użyciu programu `#` ; w ten sposób mogą pozostać w notesie jako materiał instruktażowy, ale nie zajmie się żadnym czasem i nie będzie generować niepotrzebnych danych wyjściowych. W takim przypadku zachowywanie poleceń z komentarzem w notesie wskazuje również zależności notesu.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Utwórz pozostałe komórki

Aby wypełnić resztę notesu, należy utworzyć serię promocji i komórek kodu. Dla każdej komórki wymienionej poniżej najpierw utwórz nową komórkę, a następnie ustaw typ, a następnie wklej zawartość.

Mimo że możesz poczekać na uruchomienie notesu po utworzeniu każdej komórki, warto uruchomić każdą komórkę podczas tworzenia. Nie wszystkie komórki wyświetlają dane wyjściowe; Jeśli nie widzisz żadnych błędów, założono, że komórka została normalnie uruchomiona.

Każda komórka kodu zależy od kodu, który został uruchomiony w poprzednich komórkach, i jeśli nie zostanie uruchomiona jedna z komórek, późniejsze komórki mogą spowodować błędy. Jeśli okaże się, że zapomniano uruchomić komórkę, spróbuj użyć **komórki**  >  **wszystkie powyżej** przed uruchomieniem bieżącej komórki.

Jeśli zobaczysz nieoczekiwane wyniki (prawdopodobnie będą!), sprawdź, czy w zależności od potrzeb każda komórka ma ustawioną wartość "Code" lub "replika". Na przykład błąd "Nieprawidłowa składnia" zwykle występuje po wprowadzeniu promocji w komórce kodu.

1. Komórka z promocji:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Komórka kodu; Po uruchomieniu program wyświetla zawartość tabeli jako dane wyjściowe. Możesz pominąć dane wyjściowe, dodając komentarz do `print` instrukcji.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > W tym kodzie mogą pojawić się ostrzeżenia o zmianie rozmiaru "numpy. dtype"; ostrzeżenia można bezpiecznie zignorować.

1. Komórka z promocji:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Komórka kodu; Po uruchomieniu ta komórka nie ma danych wyjściowych.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Komórka z promocji:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Komórka kodu; Po uruchomieniu w tej komórce są wyświetlane dane wyjściowe `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Komórka z promocji:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Komórka kodu; Po uruchomieniu ta komórka pokazuje wyniki podobne do `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Komórka z promocji:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Komórka z promocji:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Komórka kodu; Po uruchomieniu ta komórka tworzy graficzny wykres. Jeśli nie widzisz wykresu po raz pierwszy (a zamiast tego Zobacz "rozmiar rysunku 640x480 z 1 osiami"), ponownie uruchom komórkę.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Wykreśl dane wyjściowe z kodu matplotlib](media/tutorial/tutorial-plot-output.png)

1. Komórka z promocji:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Wyczyść dane wyjściowe i ponownie uruchom wszystkie komórki

Po wykonaniu kroków opisanych w poprzedniej sekcji w celu wypełnienia całego notesu utworzono fragment uruchomionego kodu w kontekście pełnego samouczka dotyczącego regresji liniowej. Bezpośrednia kombinacja kodu i tekstu jest jedną z doskonałych zalet notebooków!

Spróbuj ponownie uruchomić cały Notes:

1. Wyczyść wszystkie dane sesji jądra i wszystkie komórki wyjściowe, wybierając ponownie uruchom **jądro**  >  **& Wyczyść dane wyjściowe**. To polecenie jest zawsze dobrym obiektem do uruchomienia po zakończeniu pracy z notesem, tylko w celu upewnienia się, że nie utworzono żadnych niewielkich zależności między komórkami kodu.

1. **Uruchom ponownie Notes przy użyciu**opcji  >  **Uruchom wszystko**. Zauważ, że wskaźnik jądra jest wypełniany, gdy kod jest uruchomiony.

    Jeśli masz kod, który jest zbyt długi lub jest zablokowany w inny sposób, możesz zatrzymać jądro za pomocą polecenia **jądra**  >  **przerwania** .

1. Przewiń Notes, aby przeanalizować wyniki. (Jeśli wykres nie zostanie wyświetlony, należy ponownie uruchomić tę komórkę).

## <a name="save-halt-and-close-the-notebook"></a>Zapisz, zatrzymywanie i zamykanie notesu

Podczas edytowania notesu **można zapisać jego**bieżący stan za pomocą  >  polecenia**Zapisz i Utwórz punkt kontrolny** lub przycisk Zapisz na pasku narzędzi. "Punkt kontrolny" tworzy migawkę, którą można przywrócić w dowolnym momencie podczas sesji. Punkty kontrolne umożliwiają dokonanie szeregu eksperymentalnych zmian i jeśli te zmiany nie działają, można po prostu wrócić do punktu kontrolnego za pomocą **File**  >  polecenia**Przywróć plik do punktu kontrolnego** . Alternatywnym podejściem jest tworzenie dodatkowych komórek i Dodawanie komentarzy do kodu, który nie ma być uruchamiany; w dowolny sposób działa.

Możesz również użyć **File**  >  polecenia**Utwórz kopię** w dowolnym momencie, aby utworzyć kopię bieżącego stanu notesu w nowym pliku w projekcie. Ta kopia zostanie otwarta automatycznie na nowej karcie przeglądarki.

Po zakończeniu pracy z notesem Użyj **File**  >  polecenia**Zamknij i zatrzymywanie** , które powoduje zamknięcie notesu i zamknięcie jądra, na którym działa. Azure Notebooks następnie automatycznie zamknie kartę przeglądarki.

## <a name="debug-notebooks-using-visual-studio-code"></a>Debugowanie notesów przy użyciu Visual Studio Code

Jeśli komórki kodu w notesie nie zachowywać się w oczekiwany sposób, mogą wystąpić błędy kodu lub inne wady. Jednak oprócz `print` instrukcji using do wyświetlania wartości zmiennych, typowe środowisko Jupyter nie oferuje żadnych funkcji debugowania.

Na szczęście można pobrać plik *. ipynb* notesu, a następnie otworzyć go w Visual Studio Code przy użyciu rozszerzenia języka Python. Rozszerzenie bezpośrednio importuje Notes jako pojedynczy plik kodu, zachowując komórki promocji w komentarzach. Po zaimportowaniu notesu można użyć debugera Visual Studio Code do przechodzenia przez kod, ustawiania punktów przerwania, sprawdzania stanu i tak dalej. Po wprowadzeniu poprawek do kodu wyeksportuj plik *. ipynb* z Visual Studio Code i przekaż go z powrotem do Azure Notebooks.

Aby uzyskać więcej informacji, zobacz [debugowanie Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) w dokumentacji Visual Studio Code.

Zobacz też [Visual Studio Code-Jupyter support](https://code.visualstudio.com/docs/python/jupyter-support) for Visual Studio Code features for Jupyter Notess.

## <a name="next-steps"></a>Następne kroki

- [Explore sample notebooks (Eksplorowanie przykładowych notesów)](azure-notebooks-samples.md)

Artykuły z poradami:

- [Tworzenie i klonowanie projektów](create-clone-jupyter-notebooks.md)
- [Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
- [Praca z plikami danych](work-with-project-data-files.md)
- [Uzyskiwanie dostępu do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Używanie usługi Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
